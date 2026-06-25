Manifiestos de Kubernetes — Tienda de Perritos (namespace: tienda)
===================================================================

IMPORTANTE: las imagenes usan el placeholder {{ECR_URL}}.
Antes de aplicar (o mediante deploy.sh) hay que reemplazarlo por tu URL real de ECR:

  ECR_URL="<ACCOUNT_ID>.dkr.ecr.us-east-1.amazonaws.com"
  find . -type f -name "*.yaml" -exec sed -i "s|{{ECR_URL}}|${ECR_URL}|g" {} \;

Orden de aplicacion
-------------------
1) namespace.yaml
2) mysql-secret.yaml        -> Secret con MYSQL_ROOT_PASSWORD (base64)
3) mysql-deployment.yaml    -> Deployment MySQL
4) mysql-service.yaml       -> Service headless (clusterIP: None)
5) backend-deployment.yaml  -> Deployment backend (Node, puerto 3001) + probes
6) backend-service.yaml     -> Service ClusterIP (interno)
7) frontend-deployment.yaml -> Deployment frontend (nginx, puerto 80) + probes
8) frontend-service.yaml    -> Service LoadBalancer (publico)
9) backend-hpa.yaml         -> HPA backend  (min 2, max 10, CPU 70%)
10) frontend-hpa.yaml       -> HPA frontend (min 2, max 6,  CPU 60%)

El HPA requiere Metrics Server instalado en el cluster:
  kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml

Verificacion
------------
  kubectl get pods -n tienda
  kubectl get svc tienda-frontend -n tienda   (copiar EXTERNAL-IP en el navegador)
  kubectl get hpa -n tienda
