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
   >
4. Vous avez supprimé le pod `web-...` : que s'est-il passé, et pourquoi ?
   >

## Exercice 3 — Modifier un conteneur en direct

5. Après suppression du pod, votre message « Bienvenue... » a-t-il survécu ? Pourquoi ?
   >
6. Pourquoi le `port-forward` a-t-il cessé de fonctionner après la suppression du pod ?
   >

## Exercice 4 — Déclaratif

7. Quelle différence entre `kubectl create` et `kubectl apply` ?
   >
8. À quoi sert le `selector` d'un Service ? Que se passe-t-il s'il ne correspond à aucun pod ?
   >

## Exercice 5 — Scaling & mises à jour

9. Sur quels nœuds se trouvent vos 3 réplicas ? Collez la sortie de `kubectl get pods -l app=site -o wide`.
   ```

   ```
10. Avec l'image `nginx:9.99`, quel statut avait le pod ? Quelle commande vous a donné la cause ?
    >
11. Pendant la panne, le site restait-il accessible ? Pourquoi ?
    >

## Exercice 6 — Portainer & Mario

12. URL publique de votre Mario :
    >
13. Retrouvez le Deployment `mario` en ligne de commande. Portainer et `kubectl` parlent-ils au même composant ? Lequel ?
    >

## Bonus (facultatif)

14. Bonus réalisés et remarques :
    >

---

**Score obtenu avec `./scripts/check.sh` :** ... / ...
