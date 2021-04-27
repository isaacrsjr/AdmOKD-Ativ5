# AdmOKD-Ativ5

Essa atividade, realizada em 3 etapas, utiliza:

- a imagem docker disponível no [repositório docker](https://hub.docker.com/repository/docker/isaacrsjr/adm_okd_ativ2)
- o projeto [Play with Kubernetes](https://labs.play-with-k8s.com/)

## Etapa 1

No bash, foram digitados os seguintes comandos:

```bash
git clone https://github.com/isaacrsjr/AdmOKD-Ativ2.git
cd AdmOKD-Ativ2
cd app
# editado arquivo conforme mostrado abaixo
cd ..
git add .
git commit -m 'criada a aplicação blue'
git tag blue
git push origin blue
# nesse momento o hub.docker.com gera uma imagem com a tag blue
```

```python
from flask import Flask
from flask_restful import Api

app = Flask(__name__)

api = Api(app)


@app.route('/')
def hello_world():
    return 'Aplicacao Blue'


@app.after_request
def add_headers(response):
    response.headers.add('Access-Control-Allow-Origin', '*')
    response.headers.add('Access-Control-Allow-Headers', 'Content-Type,Authorization')
    return response


if __name__ == '__main__':
    app.run(debug=True, host='0.0.0.0')
```

Depois, foi criado a *'aplicacao green'* mudando o termos dos de *blue* para *green*.

## Etapa 2

Para a realização da tarefa foram criados os arquivos *deployment.yml* e *service.yml*.
O *commit* no github, referente a essa atividade é [esse aqui](https://github.com/isaacrsjr/AdmOKD-Ativ4/tree/etapa1#etapa-1).

deployment.yml:

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app-ativ5-dep
  namespace: univates
spec:
  replicas: 2
  selector:
    matchLabels:
      app: app-ativ5
  template:
    metadata:
      labels:
        app: app-ativ5
    spec:
      containers:
      - name: admokd-ativ2-container
        image: isaacrsjr/adm_okd_ativ2:blue
        ports:
          - containerPort: 5000
```

service.yml:

```yml
apiVersion: v1
kind: Service
metadata:
  name: app-ativ5-svc
  namespace: univates
spec:
  selector:
    app: app-ativ5
  ports:
  - port: 80
    targetPort: 5000
```

Comandos Executados no Master:

```bash
# obtendo arquivos dos rescursos kubernetes
cd /
git clone https://github.com/isaacrsjr/AdmOKD-Ativ5.git
cd AdmOKD-Ativ5

cat deployment.yml              # somente para exibição do conteúdo
kubectl apply -f deployment.yml

kubectl get pods --namespace=univates

cat service.yml                 # somente para exibição do conteúdo
kubectl apply -f service.yml

kubectl get services --namespace=univates
# execução do curl <clusterIP>:80 para exibir o html do container
# a porta 80 foi definida no arquivo service.yml
```

## Etapa 3

Comandos Executados no Master (depos de ter feito a etapa 1):

```bash

# para acompanhar as alteraçoes:
watch -d kubectl get pods
#
```
