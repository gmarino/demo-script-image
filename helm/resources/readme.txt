--------------------------------------------------------------------------------------------------------------------------
-- Cosa contiene lo ZIP helm-chart.zip
--------------------------------------------------------------------------------------------------------------------------
helm-chart/Chart.yaml – metadati del chart. 1
helm-chart/values.yaml – configurazioni per i 4 servizi (Eureka, Service A/B, Gateway) con variabili d’ambiente e porte. 1
helm-chart/templates/deployment.yaml – Deployment generico che itera su tutti i servizi. 1
helm-chart/templates/service.yaml – Service per ogni componente, porte mappate secondo il docker-compose. 1


# Scompatta e installa
unzip helm-chart.zip
helm install demo ./helm-chart

--------------------------------------------------------------------------------------------------------------------------
-- Cosa contiene lo ZIP helm-chart-with-ingress.zip
--------------------------------------------------------------------------------------------------------------------------
values.yaml aggiornato
Con blocco ingress: già configurato per NGINX e routing verso il gateway:

ingress.enabled: true
className: nginx
host demo.local
path / reindirizzato al servizio demo-gateway-service porta 8765

✅ templates/ingress.yaml
Template pronto per Kubernetes Ingress API networking.k8s.io/v1.
📦 Contenuto dello ZIP

Chart.yaml
values.yaml (con sezione ingress)
templates/deployment.yaml
templates/service.yaml
templates/ingress.yaml


unzip helm-chart-with-ingress.zip
helm install demo ./helm-chart


### Ricorda: per usare demo.local devi aggiungerlo nel tuo /etc/hosts
echo "127.0.0.1 demo.local" | sudo tee -a /etc/hosts

--------------------------------------------------------------------------------------------------------------------------
-- Minikube
--------------------------------------------------------------------------------------------------------------------------

# Perché funzionano su Minikube!
I file Helm che abbiamo generato usano solo:

Deployment
Service (ClusterIP)
Ingress (API: networking.k8s.io/v1)
Containers standard
Nessuna feature cloud-specific

→ Tutte queste risorse sono perfettamente supportate da Minikube, quindi sei a posto.

⚠️ L’unica cosa da controllare: Ingress NGINX su Minikube
Minikube non attiva l’ingress controller automaticamente.
Devi abilitarlo:
--> minikube addons enable ingress

Per verificare:
--> kubectl get pods -n ingress-nginx

#Se usi host come demo.local
Abbiamo messo nell’Ingress:

host: demo.local
``

# Su Minikube devi aggiungere al tuo /etc/hosts:
echo "$(minikube ip) demo.local" | sudo tee -a /etc/hosts

### Installazione completa su Minikube

unzip helm-chart-with-ingress.zip
helm install demo ./helm-chart

Test:
--> curl http://demo.local/

# Opzionale: uso del LoadBalancer su Minikube
Se ti servisse un LoadBalancer, puoi usare Minikube tunnel:
--> minikube tunnel

# Come testare l'ingress su Minikube?

1. Abilita l’ingress controller di Minikube
Minikube include già un controller NGINX come addon (non devi installare Nginx Ingress da Helm).

minikube addons enable ingress &
``

Verifica che sia in esecuzione:
kubectl get pods -n ingress-nginx

Dovresti vedere un pod simile a:

---------------------------------------------------------------------------------------------
minikube start --memory=4096 --cpus=2











