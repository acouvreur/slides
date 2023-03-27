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

# Sablier

Scale to Zero

<div class="abs-br m-6 flex gap-2">
  <a href="https://github.com/acouvreur/sablier" target="_blank" alt="GitHub"
    class="text-xl slidev-icon-btn opacity-50 !border-none !hover:text-white">
    <carbon-logo-github />
  </a>
</div>

---
layout: image-right
image: /sablier-github.png
---

# What is Sablier?

<style>
.sablier {
  color: #ffd05b;
  font-weight: bold;
  text-decoration: underline;
}
.strategy {
  font-weight: bold;
}
.go {
  color: #00ADD8
}
.description {
  opacity: 0.8
}
.highlight {
  font-weight: bold;
  text-decoration: underline;
}
.features {
  font-size: 1rem
}
</style>

<p class="description">
<span class="sablier">Sablier</span> is an API written in <span class="go">Go</span> to start containers for a given duration and <span class="highlight">scale to zero after a period of inactvity</span>
</p>


<v-clicks>

### Features

</v-clicks>

<br/>
<div class="features">

<v-clicks>

- 🔎 **Discovery** - find containers by labels/names
  ```bash
  docker run --label sablier.enable=true whoami
  ```
- 🛠 **Providers** - docker, swarm, kubernetes etc.
- 💡**Strategies** - dynamic and blocking strategies
- ⏳️**Session** - duration is extended after each call

</v-clicks>
</div>
---
layout: image-right
image: /cold-start.jpeg
---

<style>
.subtitle {
  color: #ffd05b;
}
</style>

# Scale to zero?

<v-clicks>

### 💲 <span class="subtitle">Market use cases</span>

</v-clicks>


<v-clicks>

- Google Serverless (App Engine, Function)
- AWS Lambda
- Heroku
- Github Codespace
- And more!

</v-clicks>

<br/>

<v-clicks>

### 🫵 <span class="subtitle">For you</span>

</v-clicks>

<v-clicks>

- Reduce costs on the cloud
  - QA environments rarely used 
  - Forgotten deployed services
- Selfhosting on low resources
- Free and open source software alternative

</v-clicks>

---

#  With a waiting page (Dynamic strategy)

<video controls>  
  <source src="/dynamic-strategy-demo.mp4" type="video/mp4">
</video>

---

# Hanging the request until the container is running

<video controls>  
  <source src="/blocking-strategy-demo.mp4" type="video/mp4">
</video>

---
layout: image
image: /then-what.jpeg
---

<style>
h1 {

}
</style>

# Then what?

---
layout: statement
---

# Reverse Proxy integration 

<img src="/reverse-proxy-integration.bmp" />

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

Demo time?

---
layout: image-right
image: /reverse-proxies.png
---
<style>
.nightmare {
  color: #d35721;
  font-weight: bold;
  text-decoration: underline;
}
</style>

# <span class="nightmare">Integration nightmare</span>

- Different APIs
- Different languages (Go, C, Javascript, Lua)
- Breaking changes
- Provider compatibility
- Cloud native

---
layout: image
---

<img src="/webassembly-logo.png" />

---

# What is Web Assembly

- Bytecode (almost native speed)
- Compilation target for higher level languages (Rust, Go, C++...)
- Runs in a sandboxed environment

  
<img src="/webassembly.png" />

---

# Proxy WASM (WebAssembly)

- Proxy-Wasm is a set of ABI specifications
  - L4/L7 proxies
  - Extensions delivered as WebAssembly modules
- First designed for **Envoy**
- But other projects picked it up as the ABI spec for their WASM integration

---

# Bonus features

- Custom theme

---

Any question?
