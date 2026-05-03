# Claude Code & OpenClaude — Démos pédagogiques

Dépôt de tutoriels pour apprendre à utiliser **Claude Code officiel (payant)** ou son fork open source **OpenClaude (gratuit)** comme assistant de développement dans le terminal, avec un focus sur **Python + Tkinter** pour les étudiants débutants.

---

## Convention de nommage

Chaque fichier suit le format :

```
NN-[OUTIL-PAYANT|GRATUIT]-description-longue-significative.md
```

- `NN` = numéro d'ordre (01-06)
- `[OUTIL-PAYANT|GRATUIT]` = quel outil est utilisé et son coût
- description = en kebab-case, suffisamment longue pour être compréhensible

---

## Index des fichiers

### Installation et setup (01-04)

| # | Fichier | Outil | Coût | OS | À qui ? |
|---|---------|-------|------|-----|---------|
| 01 | [Installation OpenClaude multiplateforme (Ollama / LM Studio)](./01-[OPENCLAUDE-GRATUIT]-installation-multiplatforme-ollama-lmstudio.md) | OpenClaude + Ollama ou LM Studio | Gratuit | Linux / macOS / Windows | Tous niveaux — **commence ici** |
| 02 | [Ollama : quickstart, installation et modèles](./02-[OLLAMA-GRATUIT]-quickstart-installation-modeles-locaux.md) | Ollama seul | Gratuit | Linux / macOS / Windows | Tous niveaux — installer un LLM local en 15 min |
| 03 | [llama.cpp : compilation GGUF (avancé)](./03-[LLAMACPP-GRATUIT]-compilation-gguf-multiplatforme-avance.md) | llama.cpp compilé soi-même | Gratuit | Linux / macOS / Windows | Avancé — version concise |
| 04 | [Agent local avancé : OpenClaude + llama.cpp compilé soi-même](./04-[OPENCLAUDE-GRATUIT]-agent-local-avance-llamacpp-compile-soi-meme.md) | OpenClaude + llama.cpp | Gratuit | Linux / macOS / Windows | Avancé — version verbose avec tuning, multi-GPU |

### Travaux pratiques (05-06)

| # | Fichier | Outil | Coût | OS | À qui ? |
|---|---------|-------|------|-----|---------|
| 05 | [TP : mini-app Python Tkinter avec **Claude Code (mode agent)**](./05-[CLAUDE-PAYANT]-mini-app-gestionnaire-taches-python-tkinter-mode-agent.md) | Claude Code officiel | **Payant** | Linux / macOS / Windows | Étudiants débutants ayant accès à Claude Code |
| 06 | [TP : mini-app Python Tkinter avec **OpenClaude (mode chat)**](./06-[OPENCLAUDE-GRATUIT]-mini-app-gestionnaire-taches-python-tkinter-mode-chat.md) | OpenClaude + Ollama | Gratuit | Linux / macOS / Windows | Étudiants débutants sans budget cloud |

---

## Quel parcours pour quel besoin ?

### « Je veux juste tester un agent local gratuit »
1. [02 — Ollama quickstart](./02-[OLLAMA-GRATUIT]-quickstart-installation-modeles-locaux.md) (15 min)
2. [01 — OpenClaude installation](./01-[OPENCLAUDE-GRATUIT]-installation-multiplatforme-ollama-lmstudio.md) (15 min)
3. C'est prêt.

### « Je suis prof et je veux donner un TP gratuit à mes étudiants »
1. [02 — Ollama quickstart](./02-[OLLAMA-GRATUIT]-quickstart-installation-modeles-locaux.md)
2. [01 — OpenClaude installation](./01-[OPENCLAUDE-GRATUIT]-installation-multiplatforme-ollama-lmstudio.md)
3. [06 — TP gratuit OpenClaude (mode chat)](./06-[OPENCLAUDE-GRATUIT]-mini-app-gestionnaire-taches-python-tkinter-mode-chat.md)

### « J'ai un abonnement Claude et je veux un TP plus fluide »
1. [05 — TP payant Claude Code (mode agent)](./05-[CLAUDE-PAYANT]-mini-app-gestionnaire-taches-python-tkinter-mode-agent.md)

### « Je veux comprendre la stack jusqu'au compilateur »
1. [03 — llama.cpp compilation (concis)](./03-[LLAMACPP-GRATUIT]-compilation-gguf-multiplatforme-avance.md)
2. [04 — Agent local avancé (verbose)](./04-[OPENCLAUDE-GRATUIT]-agent-local-avance-llamacpp-compile-soi-meme.md)

---

## Différence clé : mode agent (payant) vs mode chat (gratuit)

| Critère | Claude Code officiel (payant) | OpenClaude + Ollama (gratuit) |
|---------|-------------------------------|-------------------------------|
| Coût | abonnement Anthropic | gratuit |
| Modèle | Claude (cloud Anthropic) | Qwen, Gemma, Llama (local) |
| Mode | **agent** : modifie les fichiers tout seul | **chat** : vous copiez-collez le code |
| Slash commands | toutes fiables | partiellement fiables |
| Mémoire `CLAUDE.md` | chargée auto | lue à la demande |
| Skills (`SKILL.md`) | supportées | non supportées |
| Bon pour | projets réels, fluidité | apprentissage, machine sans cloud |

---

## Structure pédagogique commune à tous les fichiers

- Pitch initial (public, OS, durée, pré-requis).
- Bloc `<details>` qui pose les différences clés.
- **Sommaire ancré** avec liens vers chaque section.
- Sections numérotées avec **bouton « retour en haut »** entre chaque section.
- Commandes shell systématiquement données pour **Linux**, **macOS** et **Windows (PowerShell)**.
- **Section Troubleshooting** unique en fin de fichier.
- Pas d'emojis, langage clair, code lisible.
