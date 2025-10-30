---
title: Local Testing with Ngrok
excerpt: >-
  Learn how to test Prahsys webhooks locally using ngrok to create a secure
  tunnel to your localhost, allowing webhook testing during development
deprecated: false
hidden: false
metadata:
  title: Setup Webhooks with ngrok | Prahsys Documentation
  description: >-
    Learn how to test Prahsys webhooks locally using ngrok to create a secure
    tunnel to your localhost, allowing webhook testing during development
  keywords:
    - Prahsys webhooks
    - ngrok integration
    - webhook testing
    - localhost tunneling
    - webhook development
    - secure tunnels
    - local webhook testing
    - ngrok setup
    - Svix webhooks
    - webhook debugging
  robots: index
---
<br />

[Ngrok](https://ngrok.com/) is a tool that creates a secure tunnel to your localhost, allowing you to expose a local server to the internet.
This is particularly useful for testing webhooks and other integrations that require a publicly accessible URL.

This guide is to help you test your webhooks locally using ngrok.

### Create Your Ngrok Account

Go to <Anchor label="Ngrok" target="_blank" href="https://dashboard.ngrok.com/signup">Ngrok</Anchor> and create an account.

### Install Ngrok

Go to <Anchor label="Ngrok to Download" target="_blank" href="https://ngrok.com/download/mac-os">Ngrok to Download</Anchor>

### Start Ngrok

```shell Start Ngrok
ngrok http http://localhost:3000
```

### Grab your Forwarding Domain

Ngrok will generate a random https domain for you to use. This is the URL you will use to set up your webhook in SVIX.

> **Note:** You can upgrade your ngrok account to generate static URLs.

```shell Ngrok Tunnel
Session Status                online
Account                       ethan.bonin@prahsys.com (Plan: Pro)
Update                        update available (version 3.22.1, Ctrl-U to update)
Version                       3.19.0
Region                        United States (us)
Latency                       54ms
Web Interface                 http://127.0.0.1:4040
Forwarding                    https://randomlyGeneratedDomain.ngrok.app -> http://localhost:8080

Connections                   ttl     opn     rt1     rt5     p50     p90
                              0       0       0.00    0.00    0.00    0.00
```

### Create an endpoint with Prahsys Webhooks

Create a new webhook in the Prahsys webhooks dashboard and use that domain as the Endpoint URL to listen for incoming webhook requests.

<Image align="center" alt="Create your webhook in SVIX" border={false} caption="Prahsys Svix Webhook Management" src="https://files.readme.io/b67022166eb6d3dc74c9bbe8da1a685ef36d5e036a8927e328aacef6433eb61b-svix-webhook-create.png" width="600em" />

### Test Your Webhook Connection

When pressing _Send Example_, it will send an example body to your ngrok tunnel which will forward it to your localhost.

<Image align="center" alt="Test your webhook in SVIX" border={false} width="600em" src="https://files.readme.io/3946dd492d9529cbe07a52035f1c31bb28df3a9adb61c49c9cd3364d6e1985ad-svix-webhook-testing.png" />
