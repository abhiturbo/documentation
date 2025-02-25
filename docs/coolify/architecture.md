---
head:
  - - meta
    - name: description
      content: Coolify - Architecture
  - - meta
    - name: keywords
      content: coollabs coolify architecture
  - - meta
    - name: twitter:card
      content: summary_large_image
  - - meta
    - name: twitter:site
      content: '@andrasbacsai'
  - - meta
    - name: twitter:title
      content: Coolify
  - - meta
    - name: twitter:description
      content: An open-source & self-hostable Heroku / Netlify alternative.
  - - meta
    - name: twitter:image
      content: https://cdn.coollabs.io/assets/coollabs/og-image-documentation.png
  - - meta
    - property: og:type
      content: website
  - - meta
    - property: og:url
      content: https://coolify.io
  - - meta
    - property: og:title
      content: Coolify
  - - meta
    - property: og:description
      content: An open-source & self-hostable Heroku / Netlify alternative.
  - - meta
    - property: og:site_name
      content: Coolify
  - - meta
    - property: og:image
      content: https://cdn.coollabs.io/assets/coollabs/og-image-documentation.png
---
# Architecture

Coolify consists of four parts.

An [UI](#ui), an [API](#api), a [HTTP Proxy](#http-proxy) and optional [TCP Proxies](#tcp-proxies).

## UI
The UI you could use to configure your instance and all resources, see their status, usage, etc.

It is a static SPA site, based on [SvelteKit](https://kit.svelte.dev).

## API
The backend part of Coolify, that communicates with remote resources, with the database, with the Docker Engines, etc.

It is a backend service, based on [Fastify](https://fastify.io).

## HTTP Proxy
Coolify dynamically configures an instance of [Traefik](https://traefik.io) to generate reverse proxy configurations, generate SSL certs and route all incoming traffic to the right container.

## TCP Proxies
Databases deployed with Coolify could be reachable from over the internet. For that Coolify starts a separate instance of Traefik, to configure and proxy the TCP connections from the internet. 

### Why a separate proxy needed for this?
You can start as many databases as your server could handle, so to bind for example 100 ports to a single process could be CPU intensive task and does not make sense if you were only would like to use 10 of it. 

With this method you can dynamically bind ports on the host server, one-by-one.

### So 10 databases will create 10 Traefik instances?

Yes. Traefik is written in a way, that it use only ~10MB of memory, so it is not resource heavy.

## SSL Certs

Coolify would generate SSL certificates for your resources if you defined the URL starting with `https`. 

### Let's Encrypt
Coolify using [Let's Encrypt](https://letsencrypt.org/) helper to request, generate and store SSL certificates.

### www vs non-www
Certificates are only generated for the `defined URL`. If you specify a non-www URL, only the non-www will have a certificate. You cannot reach the www URL. 

>Usually, you need to redirect www to non-www on the DNS layer.

If you would like to generate SSL certificates for both, there is an option called `Generate SSL for www and non-www?` for applications and services.