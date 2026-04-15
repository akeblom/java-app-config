# java-app-config

GitOps-konfigurationsrepot för `java-app` — en Quarkus-baserad demoapplikation. Repot innehåller Helm-chart och ArgoCD-applikationsdefinitioner för deployment till Kubernetes.

## Struktur

```
java-app-config/
├── argocd/
│   ├── app-dev.yaml       # ArgoCD Application för dev-miljön
│   └── app-prod.yaml      # ArgoCD Application för prod-miljön
└── charts/java-app/
    ├── Chart.yaml         # Helm chart-metadata
    ├── values.yaml        # Defaultvärden
    ├── values-dev.yaml    # Overrides för dev
    ├── values-prod.yaml   # Overrides för prod
    └── templates/
        ├── deployment.yaml
        ├── service.yaml
        └── route.yaml     # OpenShift Route (valfri)
```

## Miljöer

| Miljö | Namespace       | Replicas | ArgoCD sync  |
|-------|-----------------|----------|--------------|
| dev   | `java-app-dev`  | 1        | Automatisk   |
| prod  | `java-app-prod` | 2        | Automatisk   |

## Applikationen

- Kör på port `8080`
- Health checks: `/q/health/ready` och `/q/health/live` (Quarkus standard)
- Image hämtas från `localhost:5000/java-app`
- Miljövariabeln `APP_ENV` sätts per miljö via `values-*.yaml`

## ArgoCD

ArgoCD övervakar detta repo och synkar automatiskt vid ändringar (`selfHeal: true`, `prune: true`). Namespacen skapas automatiskt om de inte finns.

För att manuellt trigga en sync:

```bash
argocd app sync java-app-dev
```

## Testa appen lokalt

Port-forwarda servicen och anropa ett endpoint:

```bash
kubectl port-forward svc/java-app-dev 9090:8080 -n java-app-dev
curl http://localhost:9090/hello
```
