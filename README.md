<p align="center">
<img src="https://cwmkt.com.br/wp-content/uploads/2024/04/logo_github.png" width="240" />
<p align="center">Seja bem-vindo ao Guia de Instalação Docker Quepasa 🚀</p>
</p>
  
<p align="center">
<img src="https://whatsapp.com/favicon.ico" alt="WhatsAPP-logo" width="32" />
<span>Grupo WhatsaAPP: </span>
<a href="https://link.cwmkt.com.br/quepasa" target="_blank">Grupo</a>
</p>

<hr />
<hr />

### Stack > add stack

![image](https://github.com/cwmkt/dockerquepasa/assets/91642837/623a6dc6-c231-4105-9a02-3070d894adb8)

```bash
version: "3.8"

services:

    quepasa:
        image: codeleaks/quepasa:latest
        restart: always
        volumes:
            - quepasa_lab_volume:/opt/quepasa
            - quepasa_lab_builder_volume:/builder
        networks:
            - ecosystem_network
        environment:
            - DOMAIN=seudominio.com.br
            - EMAIL=seuemail@seuemail.com.br
            - TZ=America/Sao_Paulo
            - APP_ENV=production
            - NODE_ENV=production
            - WEBHOOK_QUEPASA=seudominio.com.br/webhook/quepasa
            - WEBHOOK_TESTE_QUEPASA=seudominio.com.br/webhook-test/quepasa
            # Configurações quepasa
            - APP_TITLE=suaempresa
            - QUEPASA_HOST_NAME=QUEPASA
            - QUEPASA_MEMORY_LIMIT=4096M
            - QUEPASA_EXTERNAL_PORT=31000
            - QUEPASA_INTERNAL_PORT=31000
            - WEBAPIPORT=31000
            - WEBSOCKETSSL=true
            - SIGNING_SECRET=956ad4fb13adcd9a734651d33f459
            - QUEPASA_BASIC_AUTH_USER=seuemail@seuemail.com.br

            - QUEPASA_BASIC_AUTH_PASSWORD=seuemail@seuemail.com.br
            - METRICS_HOST=
            - METRICS_PORT=9392
            - MIGRATIONS=/builder/migrations
            - DEBUGJSONMESSAGES=false
            - HTTPLOGS=false
        deploy:
            mode: replicated
            replicas: 1
            placement:
                constraints:
                    - node.role == manager
                    
            labels:
                - traefik.enable=true
                - traefik.docker.network=ecosystem_network
                - traefik.http.routers.quepasa_lab.rule=Host(`seudominio.com.br`)
                - traefik.http.routers.quepasa_lab.tls=true
                - traefik.http.routers.quepasa_lab.entrypoints=web,websecure
                - traefik.http.routers.quepasa_lab.tls.certresolver=letsencryptresolver
                - traefik.http.routers.quepasa_lab.service=quepasa_lab
                - traefik.http.routers.quepasa_lab.priority=1      
                - traefik.http.middlewares.quepasa_lab.headers.SSLRedirect=true
                - traefik.http.middlewares.quepasa_lab.headers.STSSeconds=315360000
                - traefik.http.middlewares.quepasa_lab.headers.browserXSSFilter=true
                - traefik.http.middlewares.quepasa_lab.headers.contentTypeNosniff=true
                - traefik.http.middlewares.quepasa_lab.headers.forceSTSHeader=true
                - traefik.http.middlewares.quepasa_lab.headers.SSLHost=${QUEPASA_HOST}
                - traefik.http.middlewares.quepasa_lab.headers.STSIncludeSubdomains=true
                - traefik.http.middlewares.quepasa_lab.headers.STSPreload=true
                - traefik.http.services.quepasa_lab.loadbalancer.server.port=31000
                - traefik.http.services.quepasa_lab.loadbalancer.passHostHeader=true              

volumes:
  quepasa_lab_volume:
    external: true
    name: quepasa_lab_volume
  quepasa_lab_builder_volume:
    external: true
    name: quepasa_lab_builder_volume

networks:
  ecosystem_network:
    external: true
    name: ecosystem_network        
```

### Baixe os arquivos no Git oficial 

```bash
https://github.com/nocodeleaks/quepasa
```
## Suba pasta \quepasa-main\src do Quepasa dentro

```bash
/var/lib/docker/volumes/quepasa_lab_volume/_data
```















