---
title: "Mini cluster Kubernetes avec rancher (k3s) "
tags: [devops, kubernetes]
date: 2020-03-17
description: Mise en place d'un cluster kubernetes (k8s) avec k3s de rancher et multipass de canonical
author: Allemand Sébastien
---

# Découverte K3S
Suivi d'installation d'un cluster mini-kubernetes. Par curiosité je souhaite simuler des systèmes complexe.
J'aimerais découvrir de nouveaux concepts et apprendre des choses par cet apprentissage.
Ce que je partage ici n'est que des expérimentations, elles ne sont peut être  **pas poussées jusqu'au bout niviable en prod**.
Simplement des sujets que je souhaite débunker.

![](./../../../assets/devops/how-it-works-k3s.svg#center)

# Outils utilisés
- [Multipass](https://multipass.run/docs/installing-on-macos)
- [K3S](https://k3s.io/)


# Création des machines
Pour commencer nous allons créer 3 machines : 1 principales et 2 replications a l'aide de la CLI multipass

```bash
multipass launch -n basemaster --cpus 2 --mem 2G
multipass launch -n raiderone --cpus 2 --mem 2G
multipass launch -n raidertwo --cpus 2 --mem 2G
```
---
### Creation de l'instance principale
```bash
multipass --verbose exec basemaster -- sh -c "
  curl -sfL https://get.k3s.io | sh -
"
```

### Creation des noeud 2 et 3
Comme dit dans la [documentation](https://rancher.com/docs/k3s/latest/en/quick-start/).

`K3S_URL` Configure k3s en `worker mode` l'agent s'abonnera au master définit dans K3S_URL='https://$IP:6443' _(IP de basemaster)_.

`K3S_TOKEN` est le tocken d'auth stocké dans /var/lib/rancher/k3s/server/node-token du k3s principal _(basemaster dans notre exemple)_.

Pour faire ca il est possible d'utiliser les variables d'environnements
```bash
export TOKEN=$(multipass exec basemaster sudo cat /var/lib/rancher/k3s/server/node-token)
export IP=$(multipass info basemaster | grep IPv4 | awk '{print $2}')
```

### Création noeud 2
```
multipass --verbose exec raiderone -- sh -c " curl -sfL https://get.k3s.io | K3S_URL='https://$IP:6443' K3S_TOKEN='$TOKEN' sh - "
```
### Création noeud 3
```
multipass --verbose exec raidertwo -- sh -c " curl -sfL https://get.k3s.io | K3S_URL='https://$IP:6443' K3S_TOKEN='$TOKEN' sh - "
```

# Environnement k3s
Pour que kublectl(CLI kubernetes) puisse communiquer avec le k3s-master il est necessaire d'y passer le fichier décrivant l'environnement. _Avec l'ip du cluster et le token_ 🙂

Pour cela il est necessaire de copier localement la configuration du master et de remplacer l'IP locale par son IP réseau :
```
multipass exec basemaster sudo cat /etc/rancher/k3s/k3s.yaml > k3s.yaml
sed -i '' "s/127.0.0.1/$IP/" k3s.yaml
```
Note : _sed -i '' "s/schema/remplaceent" <file>_

On renseignera la configuration a utiliser via : `export KUBECONFIG=./k3s.yaml`

# Description de l'environnement

```bash
export KUBECONFIG=./k3s.yaml
kubectl top nodes

NAME         CPU(cores)   CPU%   MEMORY(bytes)   MEMORY%
basemaster   198m         9%     747Mi           37%
raidone      17m          0%     263Mi           13%
raidtwo      16m          0%     258Mi           12%
```

# Enjoy !
Votre cluster mini cluster Kubernetes est maintenant up ! 🙌
Vous pouvez maintenant déployer vos pods 👷‍♂️

### Ressources
- [Local K3s Cluster Made Easy With Multipass](https://medium.com/better-programming/local-k3s-cluster-made-easy-with-multipass-108bf6ce577c)
- [Setup cluster k3s](https://k33g.gitlab.io/articles/2020-02-21-K3S-01-CLUSTER.html)
- [Quick start k3s](https://rancher.com/docs/k3s/latest/en/quick-start/)
- [Setup a private registry on K3s](https://itnext.io/setup-a-private-registry-on-k3s-f30404f8e4d3)