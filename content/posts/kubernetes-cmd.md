---
title: "Kubernetes Commands - Toolbox "
tags: [devops, kubernetes]
date: 2020-04-07
description: Principales commandes kubernetes / Docker
author: Allemand Sébastien
---

![](./../../../assets/devops/docker_k8s.png#center)

> Collection de commandes et tips pour s'en sortir dans kubernetes


## Déploiment manuel
⛔️ **Ne jamais, jamais, jamais déployer a la main** ⛔️

Utiliser :

```bash
kubectl apply -f
```

## Investigations & debug
Quelques commande pour s'en sortir, quand ca va vraiment pas.

```bash
kubectl logs <podid>
kubectl describe pod <podid>
kubectl describe deployer <deploymentid>
kubectl describe service <serviceid>
kubectl describe ingress <ingressid>
kubectl get events
```

#### kubectl top
```bash
kubectl top <pod_name>
```

#### logs
```bash
tail -f /var/log/messages
systemctl status kubelet
journalctl -xe
```

## System
#### Alias system
Retourner toutes les ressources avec tous les namespaces
```bash
alias kall = 'kubectl get all --all-namespaces'
alias kw = 'kubectl -owide'
```

## Activer l'autocompletion
```bash
yum install bash-autocompletion
// dans .bashrc <<
source <(kubectl completion bash)
```


## Fichiers Yaml

Ne pas hésiter a regrouper plusieurs fichier Kube en un seul grace a `---`

Récupérer le Yaml d'une ressource existante :
```bash
kubectl get pod <ressource> -oyaml
```

Afficher en json et récupérer des commandes spécifiques
```bash
kubectl get nodes --output=jsonpath='{.items[0].metadata.name}'
```

## Utiliser request et limit
Détermine les ressources (CPU/ram) minimale toujours disponible (request) et limit limite les ressources dipo

_pods sans request -> premier pod premier candidats supprimés_
**Bonne pratique**: en définir

_Tips : garder des CPU REQUEST a 1, maximise les chances d'etre schédulé / scalé_
_maximise les chances de trouver un pod avec ressources dispo_

## Limit ressource quota
applique des ressources quota sur un cluster. S'applique sur un namespaces

## Utiliser propre healtchek

- readiness probes
- liveness probes

Utile dans le cas ou le container est pret mais pas l'appli (temps de demarrage de spring)
-  - InitialDelaySecond : pour décaller le demarrage des sondes et attendre que l'app soit up

kubectl exe -it <id> -c apache bash
- var d'environnement / process / connectivité reseau / filesystem & passwords / volume

## Utiliser portforward
Se connecter depuis la machine locale vers le pod a travers le kubectl
kubectl port-forward <pod> 8080:88080

possible entre services
kubectl port-forward svc/checkit 8080:808080


## Ingress
_Collection de règles permettant a une connexion externe d'atteindre des services a l'intérieur du cluster__

-  terminaison SSL
- vhost
- path spliting (ex /admin -> spécialement sur un type de services)

