# docker-knginx
Nginx(OpenResty) to Reverse Proxy Kubernetes Services

See full example here https://github.com/mingfang/jenkins-seed/blob/master/services.yml

## Knginx Service Definition
```
  - name: knginx
    ports:
      - {port: 80, name: http}
      - {port: 443, name: https}
    containers:
      - name: knginx
        image: rebelsoft.com/knginx:latest
        ports:
          - {containerPort: 80,    hostPort: 80}
          - {containerPort: 443,    hostPort: 443}
```

# Features

## Service Discovery
Sample Service Using HTTPBIN
```
  - name: httpbin
    ports:
      - port: 5000
    annotations:
      nginx:
        - http:
            - server: default
              paths:
                - path: /
    containers:
      - name: httpbin
        image: rebelsoft.com/httpbin:latest
```
### Virtual Hosting
```
..
    annotations:
      nginx:
        - http:
            - server: default
              paths:
                - path: /
        - http:
            - server: httpbin.*
              paths:
                - path: /
..                
```
### Canary Service 
Split 10% traffic from v1 to v2 
```
  - name: httpbin
    ports:
      - port: 5000
    annotations:
      nginx:
        - http:
            - server: default
              paths:
                - path: /
    containers:
      - name: httpbin
        image: rebelsoft.com/httpbin:v1
    canary:
      pct_traffic: 10
      containers:
        - name: httpbin
          image: rebelsoft.com/httpbin:v2
```

## SAML SSO
Add to Knginx Definition
```
..
        env:
          - {name: SAML_IDP_URL, value: "http://<YOUR DOMAIN>/auth/realms/master/protocol/saml/clients/saml"}
..
```
Note: The SAML URL is provided by your IDP.  Try https://github.com/mingfang/docker-keycloak.

## Pagespeed 
Add to Knginx Definition
```
..
        env:
          - {name: PAGESPEED, value: "on"}
..
```

## Letsencrypt
Add to Knginx Definition
```
..
        env:
          - {name: LETSENCRYPT_DOMAINS, value: "www.rebelsoft.com|rebelsoft.com"}
..
```

