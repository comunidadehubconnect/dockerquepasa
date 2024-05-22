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

### Caso não tenha Portainer e Traefix instalado, siga primeira etapa

<details>
<summary>Instalando Portainer e Traefix</summary>

### Atualizando Dependências

Atualize os repositórios do Ubuntu executando o seguinte comando:

```bash
sudo apt update && apt upgrade -y
```

----------------------------------------------------------------------------

**Instale o Docker em sua VPS**

```bash
sudo apt install docker.io -y
```

----------------------------------------------------------------------------

**Instalando Portainer**

```bash
docker swarm init
```

```bash
nano traefik.yml
```

```bash
version: "3.8"

services:

  traefik:
    image: traefik:2.11.1
    command:
      - "--api.dashboard=true"
      - "--providers.docker.swarmMode=true"
      - "--providers.docker.endpoint=unix:///var/run/docker.sock"
      - "--providers.docker.exposedbydefault=false"
      - "--providers.docker.network=ecosystem_network"
      - "--entrypoints.web.address=:80"
      - "--entrypoints.web.http.redirections.entryPoint.to=websecure"
      - "--entrypoints.web.http.redirections.entryPoint.scheme=https"
      - "--entrypoints.web.http.redirections.entrypoint.permanent=true"
      - "--entrypoints.websecure.address=:443"
      - "--certificatesresolvers.letsencryptresolver.acme.httpchallenge=true"
      - "--certificatesresolvers.letsencryptresolver.acme.httpchallenge.entrypoint=web"
      - "--certificatesresolvers.letsencryptresolver.acme.email=contato@seudominio.com.br"
      - "--certificatesresolvers.letsencryptresolver.acme.storage=/etc/traefik/letsencrypt/acme.json"
      - "--log.level=DEBUG"
      - "--log.format=common"
      - "--log.filePath=/var/log/traefik/traefik.log"
      - "--accesslog=true"
      - "--accesslog.filepath=/var/log/traefik/access-log"
    deploy:
      placement:
        constraints:
          - node.role == manager
      labels:
        - "traefik.enable=true"
        - "traefik.http.middlewares.redirect-https.redirectscheme.scheme=https"
        - "traefik.http.middlewares.redirect-https.redirectscheme.permanent=true"
        - "traefik.http.routers.http-catchall.rule=hostregexp(`{host:.+}`)"
        - "traefik.http.routers.http-catchall.entrypoints=web"
        - "traefik.http.routers.http-catchall.middlewares=redirect-https@docker"
        - "traefik.http.routers.http-catchall.priority=1"
    volumes:
      - "/var/run/docker.sock:/var/run/docker.sock:ro"
      - "traefik_certificates_volume:/etc/traefik/letsencrypt"
    ports:
      - target: 80
        published: 80
        mode: host
      - target: 443
        published: 443
        mode: host
    networks:
      - ecosystem_network

volumes:
  traefik_certificates_volume:
    external: true
    name: traefik_certificates_volume

networks:
  ecosystem_network:
    external: true
    name: ecosystem_network
 ```

```bash
nano portainer.yml
```

```bash
version: "3.8"

services:

  agent:
    image: portainer/agent:latest
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - /var/lib/docker/volumes:/var/lib/docker/volumes
    networks:
      - ecosystem_network
    deploy:
      mode: global
      placement:
        constraints: [node.platform.os == linux]

  portainer:
    image: portainer/portainer-ce:latest
    command: -H tcp://tasks.agent:9001 --tlsskipverify
    volumes:
      - portainer_volume:/data
    networks:
      - ecosystem_network
    deploy:
      mode: replicated
      replicas: 1
      placement:
        constraints: [node.role == manager]
      labels:
        - "traefik.enable=true"
        - "traefik.docker.network=ecosystem_network"
        - "traefik.http.routers.portainer.rule=Host(`seudominio.com.br`)"
        - "traefik.http.routers.portainer.entrypoints=websecure"
        - "traefik.http.routers.portainer.priority=1"
        - "traefik.http.routers.portainer.tls.certresolver=letsencryptresolver"
        - "traefik.http.routers.portainer.service=portainer"
        - "traefik.http.services.portainer.loadbalancer.server.port=9000"

networks:
  ecosystem_network:
    external: true
    attachable: true
    name: ecosystem_network

volumes:
  portainer_volume:
    external: true
    name: portainer_volume

 ```

```bash
```

docker swarm init
```bash
docker network create --driver=overlay ecosystem_network
```

```bash
docker stack deploy --prune --resolve-image always -c traefik.yml traefik
```

```bash
docker stack deploy --prune --resolve-image always -c portainer.yml portainer
```

Acesse URL de seu Site e Crie Usuario


</details>


### Adicione Stack abaixo, Stack > add stack

![image](https://github.com/cwmkt/dockerquepasa/assets/91642837/623a6dc6-c231-4105-9a02-3070d894adb8)

Lembre de Alterar os dados 

seuemail@seuemail.com.br<br>


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
            - APP_TITLE=QUEPASA
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
                - traefik.docker.network=sua_rede
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
    name: sua_rede 
```

Depois clique em DEPLOY

![image](https://github.com/cwmkt/dockerquepasa/assets/91642837/bdc62781-993a-4d31-b8cd-5cd6466900f5)


### Baixe os arquivos no Git oficial 

```bash
https://github.com/nocodeleaks/quepasa
```

![image](https://github.com/cwmkt/dockerquepasa/assets/91642837/9a69690d-8c7e-4ed9-a5ca-ce9632a05456)


Suba pasta \quepasa-main\src do Quepasa dentro volume

```bash
/var/lib/docker/volumes/quepasa_lab_volume/_data
```
![image](https://github.com/cwmkt/dockerquepasa/assets/91642837/41261d30-67dc-4866-a693-3d30c771e316)

Acesse: seudominio.com.br/setup

Faça seu cadastro

**Pronto tudo Funcionando** ✅😎
