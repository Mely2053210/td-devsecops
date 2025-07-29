
# Rapport d’incident et post‑mortem

## Informations générales

- **Date et heure de l’incident** : 29 juillet 2025, 12:42 CET
- **Services impactés** : API TODO
- **Gravité** : modérée

## Résumé

Une erreur HTTP 500 a été déclenchée volontairement en accédant à l’endpoint `/error`.  
L’objectif était de tester la capacité du système de monitoring (Prometheus + Grafana) à détecter et visualiser un incident sur l’application Flask.

L'erreur a été immédiatement visible dans Grafana, confirmant que le système de supervision est opérationnel.

## Timeline

| Heure (CET) | Événement                                       |
|-------------|--------------------------------------------------|
| 12:42       | Requête envoyée vers `/error`, déclenchant un code 500 |
| 12:43       | Erreur détectée dans Grafana (courbe des erreurs 500 visible) |

## Détection et diagnostic

L'incident a été identifié en observant une augmentation de la métrique `flask_http_request_total{status="500"}` dans Grafana.  
L’erreur était également visible dans l’interface Prometheus.

Cela confirme que les métriques sont bien exposées par l’application, collectées par Prometheus, et affichées correctement dans Grafana.

## Cause racine

La route `/error` dans `app.py` contient une division par zéro (`1 / 0`).  
Ce bug est intentionnel et permet de simuler une exception non gérée dans le code Python, ce qui provoque une erreur 500.

Facteurs contributifs :
- Absence de gestion d’exception (`try/except`)
- Route volontairement instable pour les tests

## Actions correctives et rétablissement

Aucune action corrective nécessaire : l’incident était simulé.  
Dans une situation réelle, une exception devrait être interceptée, loggée proprement, et un message d’erreur structuré renvoyé à l’utilisateur.

## Leçons apprises et actions de prévention

-  Le système de monitoring fonctionne correctement
-  Il serait pertinent d’ajouter une alerte dans Grafana pour prévenir en cas d’explosion des erreurs 500


