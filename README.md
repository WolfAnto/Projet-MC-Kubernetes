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
