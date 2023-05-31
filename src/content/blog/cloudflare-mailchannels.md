---
author: Yannick Seeger
pubDatetime: 2023-05-31T08:58:24+02:00
title: "Sending Emails with MailChannels API using Cloudflare Workers: A Step-by-Step Guide"
featured: false
draft: false
tags:
  - programming
ogImage: ""
description: Learn how to send emails using the MailChannels API and Cloudflare Workers with our easy step-by-step guide.
---

# MailChannels
In 2022, Cloudflare partnered with MailChannels to provide a free API for sending emails. This guide will help you set up a Cloudflare Worker to send emails using the MailChannels API.

# Setting up the Worker
Make sure you have installed wrangler (Cloudflare CLI): `pnpm i -g wrangler`

To set up the worker, follow these steps:
1. `pnpm create cloudflare`
2. Enter a worker name, for example *mailer*
3. Choose *"Hello World" script*
4. Select Typescript
5. Select *no* when asked for deploying.
6. Go in the project folder and run `pnpm add zod`. This step is optional but recommended.


## Update Code
`worker.ts`
```typescript
import { z } from "zod";

const schemaSendEmail = z.object({
  toEmail: z.string().min(1),
  toName: z.string().min(1),
  fromEmail: z.string().min(1),
  fromName: z.string().min(1),
  subject: z.string().min(1),
  body: z.object({
    type: z.enum(["text/plain", "text/html"]),
    value: z.string().min(1),
  }),
});

export interface Env {
  APPLICATION_TOKEN: string;
  DKIM_DOMAIN: string;
  DKIM_SELECTOR: string;
  DKIM_PRIVATE_KEY: string;
}

export default {
  async fetch(request: Request, env: Env, ctx: ExecutionContext): Promise<Response> {
    if (request.method !== "POST") {
      return new Response(null, {
        status: 405,
      });
    }
    if (!env.APPLICATION_TOKEN) {
      return new Response(null, {
        status: 500,
      });
    }

    if (request.headers.get("authorization") !== env.APPLICATION_TOKEN) {
      return new Response(null, {
        status: 401,
      });
    }
		
    const body = await request.json();
    const mail = schemaSendEmail.safeParse(body);
    if (!mail.success) {
      console.error(mail.error);
      return new Response(null, {
        status: 400,
      });
    }

    const req = await fetch("https://api.mailchannels.net/tx/v1/send", {
      method: "POST",
      headers: {
        "content-type": "application/json",
      },
      body: JSON.stringify({
        personalizations: [
          {
            to: [{ email: mail.data.toEmail, name: mail.data.fromEmail }],
            "dkim_domain": env.DKIM_DOMAIN,
            "dkim_selector": env.DKIM_SELECTOR,
            "dkim_private_key": env.DKIM_PRIVATE_KEY,
          },
        ],
        from: {
          email: mail.data.fromEmail,
          name: mail.data.fromName,
        },
        subject: mail.data.subject,
        content: [
          {
            type: mail.data.body.type,
            value: mail.data.body.value,
          },
        ],
       }),
    });
    return req;
  },
};
```

To deploy the worker, run `pnpm deploy` in the project folder.

## Setup DNS
To set up DNS, follow these steps:
### Creating DKIM public and private key
(from https://support.mailchannels.com/hc/en-us/articles/7122849237389)

1. Run `openssl genrsa 2048 | tee priv_key.pem | openssl rsa -outform der | openssl base64 -A > priv_key.txt`
2. Run `echo -n "v=DKIM1;p=" > pub_key_record.txt && openssl rsa -in priv_key.pem -pubout -outform der | openssl base64 -A >> pub_key_record.txt`
3. Add to DNS: `mailchannels._domainkey IN TXT "v=DKIM1; p=<content of the file pub_key_record.txt>"`

### Adding DMARC
Add to DNS: `_dmarc IN TXT "v=DMARC1;p=reject;sp=reject;pct=100;"`

## Setup Environment
You can use either the Wrangler CLI or the Cloudflare Dashboard to update your worker environment variables. Set the following environment variables:
- `APPLICATION_TOKEN`: This is a token you generate yourself and send it in the `Authorization` header alongside your request to the worker
- `DKIM_DOMAIN`: Your domain.
- `DKIM_SELECTOR`: **mailchannels**
- `DKIM_PRIVATE_KEY`: the content of `priv_key.txt`

# Sending Mails
To send an email, send a *POST* request to your worker with the following headers and body:

- Headers:
  - `Authorization`: *APPLICATION_TOKEN*
- Body:
  ```
  {
    "toEmail": "to@mail.com",
    "toName": "To Name",
    "fromEmail": "my@company.com",
    "fromName": "My Company",
    "subject": "Email Subject",
    "body": {
      "type": "text/plain", // or "text/html"
      "value": "THE mail content."
    }
  }

Congratulations, you have successfully set up a Cloudflare Worker to send emails using the MailChannels API!
