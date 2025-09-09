# Agence LLM Cloud – Déploiement 1-clic (Render Blueprint)

## Ce que vous obtenez
- **Flowise (Web UI no-code)** : vos agents (Collecte → Proposition → Stratégie → Dev → QA → Dispatcher).
- **n8n (Automations)** : handoff, emails, PDF, calendrier, CRM.
- **PostgreSQL** managé.

## Déploiement (sans terminal)
1) Sur Render.com → **New** → **Blueprint** → connectez ce repo GitHub.
2) Renseignez les variables `sync:false` des deux services (mots de passe, clés).
3) **Apply** → Render crée Flowise + n8n + Postgres automatiquement.
4) Après le 1er déploiement :
   - Ouvrez **n8n** (URL Render) → créez un compte (Basic Auth = vos variables).
   - Importez le workflow `Handoff Collecte → Proposition` (voir plus bas).
   - Dans **Flowise** : connectez-vous, créez vos flows d’agents.
   - Uploadez manuellement dans Flowise les deux fichiers de config au besoin :
     - `intents.fr.json` (phrases clés FR)
     - `catalog.yaml` (catalogue d’archétypes du Dev-Agent)

> Astuce: vous pouvez stocker `intents.fr.json` et `catalog.yaml` dans /root/.flowise via l’UI Flowise (File Manager) ou un petit Node `HTTP Request` dans n8n pour les pousser sur disque au démarrage.

## Variables à renseigner (Render → service → Environment)
### Flowise
- `FLOWISE_USERNAME` / `FLOWISE_PASSWORD` : vos identifiants d’admin Flowise.
- `OPENAI_API_KEY` : optionnel si vous utilisez OpenAI (sinon Ollama plus tard).
- `INTENTS_FILE` : `/root/.flowise/intents.fr.json`
- `CATALOG_FILE` : `/root/.flowise/catalog.yaml`

### n8n
- `N8N_BASIC_AUTH_USER` / `N8N_BASIC_AUTH_PASSWORD` : login n8n
- `N8N_ENCRYPTION_KEY` : une chaîne forte (32+ caractères)
- `WEBHOOK_URL` : mettez l’URL publique n8n générée par Render après le premier déploiement

## Premier workflow n8n (résumé)
- Webhook **/collecte/validate** : reçoit `project_id` + KHA + intention
- Si intention = “Valider” → POST **/proposition/generate** (Flowise webhook)
- Si “Modifier” → renvoi vers Collecte
- Si “Annuler” → statut = brouillon (DB)

## Notes
- Chaque push GitHub redeploie Flowise et n8n.
- Vous pouvez commencer sans OpenAI et ajouter des modèles plus tard.
