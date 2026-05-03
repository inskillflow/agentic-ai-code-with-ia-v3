# 03 — OpenRouter (gratuit) sur **Linux** : OpenClaude avec modèles cloud gratuits

> **Public** : tous niveaux.
> **Coût** : **réellement gratuit** (modèles `:free` d'OpenRouter — Llama 3.1 405B, Gemini Flash, etc.).
> **OS** : Linux.
> **Durée** : 10 min.
> **Pré-requis** : OpenClaude installé — voir [`02-[OPENCLAUDE-GRATUIT]-...`](./02-[OPENCLAUDE-GRATUIT]-installation-ollama-lmstudio.md).
> **Avantage clé** : pas besoin de GPU local, et modèles plus puissants que ce que tu peux faire tourner localement.

> Pour macOS → [`../macos/03-[OPENROUTER-GRATUIT]-...`](../macos/03-[OPENROUTER-GRATUIT]-openclaude-modeles-cloud-gratuits.md).
> Pour Windows → [`../windows/03-[OPENROUTER-GRATUIT]-...`](../windows/03-[OPENROUTER-GRATUIT]-openclaude-modeles-cloud-gratuits.md).

---

## Sommaire

1. [Qu'est-ce qu'OpenRouter ?](#1-quest-ce-quopenrouter-)
2. [Important : ce qui est *vraiment* gratuit](#2-important--ce-qui-est-vraiment-gratuit)
3. [Créer un compte OpenRouter](#3-créer-un-compte-openrouter)
4. [Générer une clé API](#4-générer-une-clé-api)
5. [Configurer OpenClaude pour OpenRouter](#5-configurer-openclaude-pour-openrouter)
6. [Choisir un modèle gratuit recommandé](#6-choisir-un-modèle-gratuit-recommandé)
7. [Variables d'environnement permanentes](#7-variables-denvironnement-permanentes)
8. [Premier test](#8-premier-test)
9. [Quotas et limites des modèles `:free`](#9-quotas-et-limites-des-modèles-free)
10. [Comparaison Ollama local vs OpenRouter cloud gratuit](#10-comparaison-ollama-local-vs-openrouter-cloud-gratuit)
11. [Troubleshooting](#troubleshooting)
12. [Suite](#suite)

---

## 1. Qu'est-ce qu'OpenRouter ?

<details>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


[OpenRouter](https://openrouter.ai) est un **proxy unifié** vers des dizaines de fournisseurs LLM (Anthropic, OpenAI, Google, Meta, Mistral, etc.). Une seule API, un seul format (compatible OpenAI), un seul facturage.

**Pourquoi c'est utile pour OpenClaude** :
- Tu obtiens des modèles **plus puissants** que Llama 7B local.
- Tu n'as **pas besoin de GPU**.
- Plusieurs modèles sont **vraiment gratuits** (avec des quotas).

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## 2. Important : ce qui est *vraiment* gratuit

<details>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


> **Honnêteté** : Claude (Sonnet, Opus, Haiku) **n'est pas gratuit** sur OpenRouter — c'est facturé au token, souvent légèrement plus cher que l'API Anthropic directe à cause de la commission OpenRouter.

**Les modèles vraiment gratuits** sur OpenRouter portent le suffixe `:free` :

| Modèle | Provider | Force |
|--------|----------|-------|
| `meta-llama/llama-3.1-405b-instruct:free` | Meta | Énorme modèle généraliste |
| `meta-llama/llama-3.3-70b-instruct:free` | Meta | Bon équilibre, multilingue |
| `google/gemini-2.0-flash-exp:free` | Google | Très rapide, contexte long |
| `qwen/qwen-2.5-coder-32b-instruct:free` | Alibaba | Spécialisé code |
| `deepseek/deepseek-chat-v3:free` | DeepSeek | Bon raisonnement |

> La liste évolue souvent. Va sur [openrouter.ai/models?max_price=0](https://openrouter.ai/models?max_price=0) pour la liste à jour.

OpenRouter offre aussi ~5$ de crédits gratuits initiaux pour tester les modèles payants, mais ça part vite.

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## 3. Créer un compte OpenRouter

<details>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


1. Va sur [openrouter.ai](https://openrouter.ai).
2. Clique **Sign in** → choisis Google, GitHub, ou e-mail.
3. Confirme ton e-mail.

Aucune carte bancaire n'est requise pour utiliser **uniquement** les modèles `:free`.

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## 4. Générer une clé API

<details>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


1. Va sur [openrouter.ai/keys](https://openrouter.ai/keys).
2. Clique **Create Key**.
3. Donne un nom (ex. `openclaude-linux`).
4. Optionnel : fixe une **limite de crédit** (ex. 0$ si tu veux te restreindre aux modèles `:free`).
5. Clique **Create**.
6. **Copie immédiatement** la clé `sk-or-v1-...` (elle ne sera plus affichée).

Stocke-la dans un endroit sûr.

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## 5. Configurer OpenClaude pour OpenRouter

<details>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


### Variables d'environnement (session)

```bash
export OPENAI_API_KEY="sk-or-v1-XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
export OPENAI_BASE_URL="https://openrouter.ai/api/v1"
export OPENAI_MODEL="meta-llama/llama-3.1-405b-instruct:free"
```

| Variable | Valeur |
|----------|--------|
| `OPENAI_API_KEY` | ta clé `sk-or-v1-...` |
| `OPENAI_BASE_URL` | `https://openrouter.ai/api/v1` (HTTPS, pas HTTP !) |
| `OPENAI_MODEL` | nom complet `provider/modele:tag`, suffixe `:free` pour la gratuité |

### Lance OpenClaude

```bash
openclaude
```

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## 6. Choisir un modèle gratuit recommandé

<details>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


| Cas d'usage | Modèle recommandé |
|-------------|-------------------|
| Discussion généraliste, multilingue | `meta-llama/llama-3.3-70b-instruct:free` |
| Code Python, refacto | `qwen/qwen-2.5-coder-32b-instruct:free` |
| Très long contexte (lecture de gros fichiers) | `google/gemini-2.0-flash-exp:free` |
| Maximum de qualité, peu importe la lenteur | `meta-llama/llama-3.1-405b-instruct:free` |
| Raisonnement complexe | `deepseek/deepseek-chat-v3:free` |

Pour switcher de modèle, il suffit de changer `OPENAI_MODEL` et de relancer `openclaude`.

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## 7. Variables d'environnement permanentes

<details>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


### bash (`~/.bashrc`)
```bash
nano ~/.bashrc
# Ajoute à la fin :
export OPENAI_API_KEY="sk-or-v1-XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
export OPENAI_BASE_URL="https://openrouter.ai/api/v1"
export OPENAI_MODEL="qwen/qwen-2.5-coder-32b-instruct:free"

# Recharge
source ~/.bashrc
```

### zsh (`~/.zshrc`)
Idem dans `~/.zshrc`.

### Per-projet via `.env`
```bash
cat > .env <<'EOF'
OPENAI_API_KEY=sk-or-v1-XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
OPENAI_BASE_URL=https://openrouter.ai/api/v1
OPENAI_MODEL=qwen/qwen-2.5-coder-32b-instruct:free
EOF

set -a; source .env; set +a; openclaude
```

> **Sécurité** : ajoute `.env` à ton `.gitignore` pour ne **jamais** committer ta clé API.

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## 8. Premier test

<details>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


Dans OpenClaude :

```
dis-moi bonjour en français en 3 phrases
```

Puis :

```
quel modèle es-tu et qui t'a entraîné ?
```

Vérification directe en HTTP :

```bash
curl https://openrouter.ai/api/v1/chat/completions \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "meta-llama/llama-3.3-70b-instruct:free",
    "messages": [{"role": "user", "content": "dis bonjour"}]
  }'
```

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## 9. Quotas et limites des modèles `:free`

<details>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


OpenRouter applique des **rate limits** sur les modèles `:free` :

- environ **20 requêtes par minute** par modèle ;
- environ **200 requêtes par jour** au total sur les modèles `:free` ;
- les détails sont sur [openrouter.ai/docs](https://openrouter.ai/docs/limits).

Quand tu dépasses, tu reçois un `429 Too Many Requests`. Attends une minute puis réessaie.

> Si tu fais un TP intensif et que tu te fais bloquer, bascule temporairement sur Ollama local (tuto 02) — c'est sans quota.

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## 10. Comparaison Ollama local vs OpenRouter cloud gratuit

<details>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


| Critère | Ollama local | OpenRouter `:free` |
|---------|--------------|--------------------|
| Coût | gratuit | gratuit (avec quotas) |
| Connexion internet | non requise | requise |
| GPU | recommandé | inutile |
| Confidentialité | totale (rien ne sort) | tout passe par OpenRouter |
| Taille de modèles | ~7-14 B sur 8 Go VRAM | jusqu'à 405 B |
| Quotas | aucun | ~20 req/min, 200 req/jour |
| Latence | très basse (local) | moyenne (cloud) |
| Idéal pour | dev intensif, données sensibles | démos, étudiants sans GPU |

> **Conseil** : si ton GPU est faible mais que ton internet est bon, OpenRouter `:free` te donnera un meilleur résultat qu'Ollama avec un petit modèle local.

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## Troubleshooting

<details>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


| Symptôme | Cause | Correctif |
|----------|-------|-----------|
| `401 Unauthorized` | Clé API absente ou mauvaise | Vérifie `echo $OPENAI_API_KEY` ; régénère si besoin sur [openrouter.ai/keys](https://openrouter.ai/keys) |
| `404 model not found` | Nom de modèle erroné ou modèle plus disponible | Va sur [openrouter.ai/models?max_price=0](https://openrouter.ai/models?max_price=0) pour le nom **exact** |
| `429 Too Many Requests` | Rate limit `:free` dépassé | Attends 1 min ; ou bascule sur Ollama (tuto 02) |
| `402 Payment Required` | Tu utilises un modèle payant sans crédits | Passe à un modèle `:free` ou recharge ton compte |
| HTTP au lieu de HTTPS | URL erronée | `OPENAI_BASE_URL=https://openrouter.ai/api/v1` (pas `http://`) |
| `OPENAI_MODEL` ignoré | `~/.claude.json` prend le dessus | Édite `~/.claude.json` ou supprime-le pour repartir des variables d'env |
| Le modèle parle anglais alors que tu écris en français | Modèle moins multilingue (Llama 3.1) | Utilise `meta-llama/llama-3.3-70b-instruct:free` ou `qwen/...` |

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## Suite

<details>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


- [`07-[OPENROUTER-GRATUIT]-tp-mini-app-python-tkinter-mode-chat-cloud.md`](./07-[OPENROUTER-GRATUIT]-tp-mini-app-python-tkinter-mode-chat-cloud.md) — TP mini-app Python avec OpenRouter (cloud gratuit)
- [`02-[OPENCLAUDE-GRATUIT]-installation-ollama-lmstudio.md`](./02-[OPENCLAUDE-GRATUIT]-installation-ollama-lmstudio.md) — alternative locale (Ollama)
- [Documentation OpenRouter](https://openrouter.ai/docs)
- [Liste des modèles gratuits](https://openrouter.ai/models?max_price=0)

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>
