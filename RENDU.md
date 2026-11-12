# Rendu — Atelier Kubernetes

> Complétez ce fichier au fil de l'atelier, puis faites un commit et un push (voir la section « Rendu » du README).

**Nom / Prénom :**
**Groupe :**

---

## Exercice 1 — Cluster

1. Combien de nœuds affiche `kubectl get nodes` ? Quel est le rôle de chacun ?

Le cluster Kubernetes contient 3 nœuds :

   - k3d-lab-server-0 : nœud control-plane (master). Il gère le cluster Kubernetes, son état et l'orchestration des applications.
   - k3d-lab-agent-0 : nœud worker chargé d'exécuter les applications dans des pods.
   - k3d-lab-agent-1 : deuxième nœud worker, qui exécute également les applications.

Le cluster possède donc 1 control-plane et 2 workers.

2. Que montre `docker ps` ? Qu'est-ce qu'un « nœud » dans k3d, concrètement ?
La commande docker ps affiche 4 conteneurs Docker en cours d'exécution.

- k3d-lab-server-0 correspond au control-plane.
- k3d-lab-agent-0 et k3d-lab-agent-1 correspondent aux deux workers.
- k3d-lab-serverlb est le load balancer qui permet de rediriger le trafic vers le cluster.

Dans k3d, un nœud Kubernetes est un conteneur Docker exécutant k3s, version légère de Kubernetes. Permettant de simuler un cluster de plusieurs machines sur un seul environnement Linux.

## Exercice 2 — Premier déploiement

3. Quels objets Kubernetes ont été créés par `kubectl create deployment` ? (indice : `kubectl get all`)
La commande kubectl create deployment web --image=nginx crée un Deployment nommé web, qui crée à son tour un ReplicaSet chargé de maintenir un pod exécutant nginx.

La commande kubectl expose deployment web --port=80 crée ensuite un Service de type ClusterIP, nommé web, qui permet d'accéder à l'application sur le port 80 depuis l'intérieur du cluster.

On obtient un Deployment, un ReplicaSet, un Pod et un Service.

4. Vous avez supprimé le pod `web-...` : que s'est-il passé, et pourquoi ?
Après la suppression du pod web-68d995574f-jkrh8, Kubernetes a automatiquement créé un nouveau pod nommé web-68d995574f-6hvp4.

L'ancien pod est passé à l'état Terminating, tandis que le nouveau est passé par les états Pending et ContainerCreating avant d'atteindre Running.

Grâce au ReplicaSet, qui veille à maintenir le nombre de pods défini par le Deployment, ici 1 réplica. Kubernetes détecte donc qu'un pod manque et en recrée un automatiquement.

## Exercice 3 — Modifier un conteneur en direct

5. Après suppression du pod, votre message « Bienvenue... » a-t-il survécu ? Pourquoi ?
Non, le message « Bienvenue sur mon site Web » n'a pas survécu à la suppression du pod. Kubernetes a recréé un nouveau pod à partir de l'image nginx d'origine. Comme la modification avait été faite directement dans l'ancien conteneur, elle a été perdue.

6. Pourquoi le `port-forward` a-t-il cessé de fonctionner après la suppression du pod ?
Le port-forward a cessé de fonctionner parce qu'il était connecté au pod supprimé. Même si Kubernetes crée automatiquement un nouveau pod, le tunnel ne se reconnecte pas tout seul. Il faut donc relancer la commande port-forward.

## Exercice 4 — Déclaratif

7. Quelle différence entre `kubectl create` et `kubectl apply` ?
- kubectl create permet de créer une ressource Kubernetes de manière impérative, en donnant directement une instruction au cluster.

- kubectl apply permet de créer ou de mettre à jour des ressources à partir de fichiers YAML qui décrivent l'état souhaité.

Dans notre cas, nous avons utilisé kubectl apply pour déployer notre site et mettre à jour la ConfigMap sans recréer le pod ni interrompre le port-forward.

8. À quoi sert le `selector` d'un Service ? Que se passe-t-il s'il ne correspond à aucun pod ?
Le selector d'un Service permet d'identifier les pods vers lesquels envoyer le trafic réseau grâce à leurs labels.Dans notre cas, le Service site utilise le selector app: site pour cibler les pods possédant ce label.

Si le selector ne correspond à aucun pod, le Service ne trouve aucune destination vers laquelle envoyer les requêtes. Le Service existe toujours, mais l'application n'est plus accessible à travers lui.

## Exercice 5 — Scaling & mises à jour

9. Sur quels nœuds se trouvent vos 3 réplicas ? Collez la sortie de `kubectl get pods -l app=site -o wide`.
```
site-75775484b-2hj6g   1/1     Running   0          8m31s   10.42.1.6   k3d-lab-server-0   <none>           <none>
site-75775484b-44cn7   1/1     Running   0          37s     10.42.3.6   k3d-lab-agent-1    <none>           <none>
site-75775484b-x29hs   1/1     Running   0          37s     10.42.0.7   k3d-lab-agent-0    <none>           <none>
```

Les 3 réplicas sont répartis sur 3 nœuds différents : k3d-lab-server-0 (control-plane), k3d-lab-agent-0 (worker) et k3d-lab-agent-1 (worker).

10. Avec l'image `nginx:9.99`, quel statut avait le pod ? Quelle commande vous a donné la cause ?
Le nouveau pod était en statut ImagePullBackOff car l'image nginx:9.99 n'existe pas. La commande kubectl describe pod a permis d'identifier la cause dans la section Events.

11. Pendant la panne, le site restait-il accessible ? Pourquoi ?
Oui, le site restait accessible car Kubernetes conservait les anciens pods fonctionnels pendant la mise à jour. Le rolling update permet ainsi d'éviter une interruption du service même si la nouvelle version échoue.

## Exercice 6 — Portainer & Mario

12. URL publique de votre Mario :
https://animated-orbit-v66gg7676pxgcprvv-8585.app.github.dev/

13. Retrouvez le Deployment `mario` en ligne de commande. Portainer et `kubectl` parlent-ils au même composant ? Lequel ?
Oui, Portainer et kubectl communiquent avec le même composant : l'API Server Kubernetes, qui fait partie du Control Plane. Ils permettent tous les deux de gérer les ressources du cluster, comme les Deployments, les Services et les Pods.

La différence est que Portainer utilise une interface graphique, tandis que kubectl utilise des commandes dans le terminal.

## Bonus (facultatif)

14. Bonus réalisés et remarques :
    >

---

**Score obtenu avec `./scripts/check.sh` :** ... / ...
