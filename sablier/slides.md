---
theme: default
background: /cover.jpeg
class: 'text-right'

# https://sli.dev/custom/highlighters.html
highlighter: shiki
# show line numbers in code blocks
lineNumbers: false

# persist drawings in exports and build
drawings:
  persist: false
# page transition

transition: slide-left
---

<style>
.sablier {
  color: #ffd05b;
  font-weight: bold;
}
</style>

# Sablier

Scale to zero

<div class="abs-br m-6 flex gap-2">
  <a href="https://github.com/acouvreur/sablier" target="_blank" alt="GitHub"
    class="text-xl slidev-icon-btn opacity-50 !border-none !hover:text-white">
    <carbon-logo-github />
  </a>
</div>

---
layout: intro
---

Alexis Couvreur

---
layout: fact
---
# Scale to zero?

Scaling to zero allows workloads to be run only when there is demand

---
layout: two-cols
---

# They scale to zero

- Google Serverless (App Engine, Function)
- AWS Lambda
- Heroku
- Github Codespace
- And more!

::right::

<img src="/they-use-it.png" />

---
layout: fact
---
# <span class="sablier">Sablier</span>

A free and open source software scaling to zero solution

---
layout: statement
---

# How does it work?

---
layout: statement
---

# Reverse proxy integration

<br/>
<br/>

<img src="/reverse-proxy-integration.png" />

---
layout: statement
---

# How to use <span class="sablier">Sablier</span>?
Let's see its integration with Caddy!

---
layout: two-cols
transition: fade
---

<style>
  .code-left {
    margin-right: 1rem
  }
</style>

# docker-compose.yaml

<div class="code-left">

```yaml
services:
  proxy:
    image: caddy:2.6.4
    ports:
      - "8080:80"
    volumes:
      - ./Caddyfile:/etc/caddy/Caddyfile:ro

  whoami:
    image: containous/whoami:v1.5.0
```

</div>

::right::

# Caddyfile


```nginx
:80 {
	route /whoami {
		reverse_proxy whoami:80
	}
}
```

---
layout: two-cols
---
<style>
  .code-left {
    margin-right: 1rem
  }
</style>

# docker-compose.yaml

<div class="code-left">

```yaml
services:
  proxy:
    image: caddy:local
    ports:
      - "8080:80"
    volumes:
      - ./Caddyfile:/etc/caddy/Caddyfile:ro

  whoami:
    image: containous/whoami:v1.5.0
    labels:
      - sablier.enable=true
      - sablier.group=demo
  
  sablier:
    image: acouvreur/sablier:1.3.0
    volumes:
      - '/var/run/docker.sock:/var/run/docker.sock'
```

</div>

::right::

# Caddyfile

```nginx
:80 {
	route /whoami {
      sablier url=http://sablier:10000 
              group=demo 
              session_duration=1m
              dynamic.display_name=Dynamic-Whoami

	  reverse_proxy whoami:80
	}
}
```

---
layout: statement
---

# Demo

---
layout: statement
---

# <span class="sablier">Sablier</span> features

---
layout: two-cols
---

# Providers

- Docker
- Docker Swarm
- Kubernetes
- Podman
- AWS EC2


::right::

<img src="/providers.png" />

---
layout: two-cols
---

# Label filtering

- Docker labels
- Docker swarm service labels
- Kubernetes deployments labels

::right::

## Docker labels

```yaml
services:
  whoami:
    image: containous/whoami:v1.5.0
    labels:
      - sablier.enable=true
      - sablier.group=mygroup
```

## Kubernetes deployments labels

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: whoami
  labels:
    app: whoami
    sablier.enable: true
    sablier.group: mygroup
```

---
layout: two-cols
---

# Reverse proxies

- Traefik (Go)
- Caddy (Go)
- Nginx (Javascript/Lua/C)
- more to come...


::right::

<img src="/reverse-proxies-implem.png" class="m-40 h-40 rounded shadow" />

---
layout: statement
---

# Integration nightmare
Develop and maintain an integration for each reverse proxy

---
layout: image-right
image: /reverse-proxies.png
---

<v-clicks>

- Different APIs
- Different languages (Go, C, Javascript, Lua)
  - Runtime plugin execution
  - Compilation plugin (recompile the proxy)
- Breaking changes
- Provider compatibility
- Cloud native

</v-clicks>

---
layout: statement
---

# WebAssembly to the rescue
WebAssembly (abbreviated Wasm) is a binary instruction format designed as a portable compilation target for programming languages, enabling deployment on the web for client and server applications.

---

# What is Web Assembly

- Bytecode (almost native speed)
- Compilation target for higher level languages (Rust, Go, C++...)
- Runs in a sandboxed environment

  
<img src="/webassembly.png" />

---
layout: two-cols
---

# Proxy WASM

- Proxy-Wasm is a set of ABI specifications
  - L4/L7 proxies
  - Extensions delivered as WebAssembly modules
- First designed for **Envoy**
- But other projects picked it up as the ABI spec for their WASM integration

---

Any question?
