# Projet-MC-Kubernetes
Projet réaliser dans le cadre de la LP A2SR.

# Procédure Docker / Kubernetes
## Installer Docker Desktop

![image](https://user-images.githubusercontent.com/73076854/220911935-271707c6-f2db-4809-884b-64bb9d707ca0.png)

## Activer Kubernetes

![image](https://user-images.githubusercontent.com/73076854/220911992-b651b34e-2db6-41e7-b6fe-597693af8d5b.png)

## Mettre à jour Kubernetes
```powershell
winget install -e --id Kubernetes.kubectl
```
![image](https://user-images.githubusercontent.com/73076854/220912055-35a095bd-ddf2-4bcf-868e-01f337456b57.png)

## Installer Kubernetes-Dashboard
```powershell
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml
```

## Lancer le proxy Kubernetes
```powershell
kubectl proxy
```
![image](https://user-images.githubusercontent.com/73076854/220912329-1e4455ed-8ff0-40bb-a37b-0ad1ad695de4.png)

## Ouvrir dans le navigateur 
```html
http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/
```

## Création d'un compte "Service Account" et d'un ClusterRoleBinding
Créer un fichier user.yaml
```yaml
---
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kubernetes-dashboard
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-user
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: admin-user
  namespace: kubernetes-dashboard
```
## Appliquer les YAML
```bash
kubectl apply -f user.yaml
```
## Génération du token de connexion
```powershell
kubectl -n kubernetes-dashboard create token admin-user
```
## Insérer le token générer dans le navigateur (Méthode Jeton) :
```bash
eyJhbGciOiJSUzI1NiIsImtpZCI6IiJ9.......
```
![image](https://user-images.githubusercontent.com/73076854/220912911-b6f6ac53-3357-4135-95f0-aa55e4f50253.png)

## Insérer le token générer dans le navigateur (Méthode Kubeconfig) :
```powershell
$TOKEN="eyJhbGciOiJSUzI1NiIsImtpZCI6IiJ9......."
kubectl config set-credentials docker-for-desktop --token="${TOKEN}"
kubectl config set-credentials docker-desktop --token="${TOKEN}"
```
![image](https://user-images.githubusercontent.com/73076854/220913036-9dfd663e-aa06-477f-addf-edc9b8fab474.png)

## Insérer le token générer dans le navigateur (Méthode 2 Kubeconfig) :
Editer le fichier de config dans C:\Users\Username\.kube\config
Chercher les lignes :
```yaml
[…]
- name: docker-desktop
  user:
    client-certificate-data: LS0tLS1CRUdJTiBDRV…
[…]
```
Ajouter la ligne suivante :
```yaml
[…]
- name: docker-desktop
  user:
    client-certificate-data: LS0tLS1CRUdJTiBDRV…
    token: eyJhbGciOiJSUzI1NiIs…
[…]
```

## (BONUS) Pour supprimer ServiceAccount et ClusterRoleBinding :
```powershell
kubectl -n kubernetes-dashboard delete serviceaccount admin-user
kubectl -n kubernetes-dashboard delete clusterrolebinding admin-user
```

## Voici l’aperçu de l’interface Kubernetes Dashboard
![image](https://user-images.githubusercontent.com/73076854/220913354-aae580d0-e615-46fc-9ffb-0b4fc4dc4b0c.png)

## Déployer le fichier minecraft.yaml
```bash
kubectl apply -f minecraft.yaml
```

## Déployer le fichier apache2.yaml
```bash
kubectl apply -f apache2.yaml
```

## Résultats
# Webite
![image](https://user-images.githubusercontent.com/73076854/227536640-0623106b-2633-4173-8c09-00f55fee022d.png)
![image](https://user-images.githubusercontent.com/73076854/227536730-36f1f742-844e-4ece-b7d9-848c48cfcee8.png)
![image](https://user-images.githubusercontent.com/73076854/227536751-78c05d25-6eae-4bc7-9571-7341970b829c.png)
![image](https://user-images.githubusercontent.com/73076854/227536763-27350cf8-8c50-40cf-80d3-920c0ffcf21b.png)
![image](https://user-images.githubusercontent.com/73076854/227536775-c62e8705-8a10-4d44-a408-221925e6f9c6.png)

# Minecraft Server
![image](https://user-images.githubusercontent.com/73076854/227536682-5d95ec18-0885-4aa4-8e08-d9f05336864b.png)

# Credits

https://github.com/rickklaasboer/Onepage-Minecraft-Website

https://github.com/itzg/docker-minecraft-server

# Explication

Explication en détail des fichiers de configuration pour le projet consistant à déployer un cluster de jeux multijoueurs sur Kubernetes.

- Fichier de configuration du déploiement Minecraft :

Le fichier de configuration du déploiement est utilisé pour décrire comment un ensemble de conteneurs doit être déployé dans un cluster de Kubernetes. Dans ce fichier YAML, nous avons spécifié les détails suivants :

- apiVersion : la version de l'API Kubernetes utilisée pour ce déploiement.
- kind : le type de ressource Kubernetes, qui dans ce cas est un Deployment.
- metadata : des métadonnées supplémentaires pour le déploiement, telles que le nom du déploiement.
- spec : la spécification du déploiement, qui comprend :
- selector : les étiquettes à utiliser pour sélectionner les pods gérés par ce déploiement.
- replicas : le nombre de réplicas à créer pour les pods gérés par ce déploiement.
- template : la définition d'un pod, qui comprend :
- metadata : des métadonnées supplémentaires pour le pod, telles que les étiquettes.
- spec : la spécification du pod, qui comprend :
- containers : la liste des conteneurs à exécuter dans le pod.
- name : le nom du conteneur.
- image : l'image Docker à utiliser pour le conteneur.
- ports : la liste des ports à exposer sur le conteneur.
- env : la liste des variables d'environnement à définir pour le conteneur.

Dans ce fichier de configuration, nous avons utilisé l'image Docker itzg/minecraft-server, qui est un serveur Minecraft prêt à l'emploi. Nous avons également défini une variable d'environnement EULA sur TRUE, ce qui indique que nous acceptons les termes du contrat de licence utilisateur final de Minecraft. Enfin, nous avons défini la taille de la mémoire à allouer pour le serveur Minecraft sur 2G.

- Fichier de configuration du service Minecraft :

Le fichier de configuration du service est utilisé pour exposer un ensemble de pods en tant que service dans un cluster de Kubernetes. Dans ce fichier YAML, nous avons spécifié les détails suivants :

- apiVersion : la version de l'API Kubernetes utilisée pour ce service.
- kind : le type de ressource Kubernetes, qui dans ce cas est un Service.
- metadata : des métadonnées supplémentaires pour le service, telles que le nom du service.
- spec : la spécification du service, qui comprend :
- selector : les étiquettes à utiliser pour sélectionner les pods à exposer en tant que service.
- ports : la liste des ports à exposer sur le service.
- name : le nom du port.
- port : le numéro de port à utiliser pour le service.
- targetPort : le numéro de port à utiliser pour les pods sélectionnés.
- type : le type de service à créer. Dans ce cas, nous avons choisi LoadBalancer, ce qui permettra de créer une adresse IP externe pour accéder au serveur Minecraft.

En résumé, les 2 fichiers de configuration sont utilisés pour déployer un serveur Minecraft dans un cluster Kubernetes, exposer le serveur en tant que service et le rendre accessible à l'extérieur du cluster via une adresse IP publique et une URL personnalisée.
