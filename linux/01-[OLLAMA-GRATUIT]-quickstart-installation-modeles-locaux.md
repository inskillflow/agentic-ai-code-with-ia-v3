# 01 — Ollama (gratuit) sur **Linux** : installation et modèles locaux

> **Public** : tous niveaux.
> **Coût** : gratuit (modèles tournent en local).
> **OS** : Linux (Ubuntu, Debian, Fedora, Arch, autres).
> **Durée** : 15 à 30 min.
> **Pré-requis** : au moins 4 Go de RAM/VRAM disponible. GPU NVIDIA recommandé pour la performance.

> Pour macOS → [`../macos/01-[OLLAMA-GRATUIT]-...`](../macos/01-[OLLAMA-GRATUIT]-quickstart-installation-modeles-locaux.md).
> Pour Windows → [`../windows/01-[OLLAMA-GRATUIT]-...`](../windows/01-[OLLAMA-GRATUIT]-quickstart-installation-modeles-locaux.md).

---

## Sommaire

1. [Étape 1 — Installer Ollama](#étape-1--installer-ollama)
2. [Étape 2 — Vérifier que ça marche](#étape-2--vérifier-que-ça-marche)
3. [Étape 3 — Vérifier que le serveur tourne](#étape-3--vérifier-que-le-serveur-tourne)
4. [Étape 4 — Méthode A : modèle de la bibliothèque Ollama](#étape-4--méthode-a--modèle-de-la-bibliothèque-ollama)
5. [Étape 5 — Méthode B : modèle custom depuis Hugging Face](#étape-5--méthode-b--modèle-custom-depuis-hugging-face)
6. [Étape 6 — Modelfile avec paramètres (optionnel)](#étape-6--modelfile-avec-paramètres-optionnel)
7. [Étape 7 — Cheatsheet des commandes utiles](#étape-7--cheatsheet-des-commandes-utiles)
8. [Étape 8 — Dans le chat Ollama (commandes slash)](#étape-8--dans-le-chat-ollama-commandes-slash)
9. [Étape 9 — Tester l'API depuis un autre programme](#étape-9--tester-lapi-depuis-un-autre-programme)
10. [Récap : le flux complet en une page](#récap--le-flux-complet-en-une-page)
11. [Troubleshooting](#troubleshooting)
12. [Suite](#suite)

---

## Étape 1 — Installer Ollama

<details>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


Une seule commande, valable sur Ubuntu, Debian, Fedora, Arch et la plupart des autres distributions :

```bash
curl -fsSL https://ollama.com/install.sh | sh
```

Le script :
- télécharge le binaire `ollama` dans `/usr/local/bin` ;
- installe un service systemd (`/etc/systemd/system/ollama.service`) ;
- crée un utilisateur système `ollama`.

Si tu n'es pas root, le script demande ton mot de passe sudo.

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## Étape 2 — Vérifier que ça marche

<details>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


```bash
ollama --version
```

Tu dois voir `ollama version is 0.x.x`. Si non → ferme et rouvre ton terminal pour recharger le PATH.

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## Étape 3 — Vérifier que le serveur tourne

<details>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


Ollama lance un serveur HTTP sur `127.0.0.1:11434` automatiquement, géré par systemd.

```bash
systemctl status ollama
```

Tu dois voir `active (running)`. Si non :

```bash
sudo systemctl start ollama
sudo systemctl enable ollama   # démarrage auto au boot
```

Test de l'API :

```bash
curl http://127.0.0.1:11434/api/tags
```

Si ça répond avec un JSON → OK.

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## Étape 4 — Méthode A : modèle de la bibliothèque Ollama

<details>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


Pour tous les modèles listés sur [ollama.com/library](https://ollama.com/library).

### Télécharger

```bash
ollama pull qwen2.5:7b
```

### Lancer

```bash
ollama run qwen2.5:7b
```

Tape ta question. Sors avec `/bye`.

### Modèles recommandés selon ta machine

| RAM/VRAM | Modèle | Commande |
|----------|--------|----------|
| 4 Go | gemma2:2b | `ollama pull gemma2:2b` |
| 8 Go | qwen2.5:7b | `ollama pull qwen2.5:7b` |
| 8 Go (code) | qwen2.5-coder:7b | `ollama pull qwen2.5-coder:7b` |
| 16 Go | qwen2.5:14b | `ollama pull qwen2.5:14b` |

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## Étape 5 — Méthode B : modèle custom depuis Hugging Face

<details>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


Pour les modèles qui **ne sont pas** dans la bibliothèque Ollama (uncensored, finetunes, modèles exotiques).

### 5.1 Télécharger le `.gguf` depuis HuggingFace

Va sur la page du modèle, onglet **Files and versions**, télécharge un `.gguf` selon ta VRAM :

- 4 Go → `*-Q4_K_M.gguf`
- 8 Go → `*-Q6_K_P.gguf`
- 12 Go+ → `*-Q8_0.gguf`

Place le fichier dans un dossier dédié :

```bash
mkdir -p ~/models/gemma-local
mv ~/Téléchargements/Gemma-4-E4B-Uncensored-Q6_K_P.gguf ~/models/gemma-local/
cd ~/models/gemma-local
```

### 5.2 Créer le Modelfile

Dans le **même dossier** que le `.gguf`, crée un fichier `Modelfile` (sans extension) :

```bash
echo "FROM ./Gemma-4-E4B-Uncensored-Q6_K_P.gguf" > Modelfile
```

### 5.3 Vérifier

```bash
cat Modelfile
ls *.gguf
```

Tu dois voir le `Modelfile` (sans extension) et le `.gguf`.

### 5.4 Enregistrer le modèle dans Ollama

```bash
ollama create gemma-local -f Modelfile
```

Attends `success` (30 s à 2 min selon la taille).

### 5.5 Lancer

```bash
ollama run gemma-local
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

## Étape 6 — Modelfile avec paramètres (optionnel)

<details>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


Pour personnaliser la temperature, le contexte, le prompt système :

```
FROM ./Gemma-4-E4B-Uncensored-Q6_K_P.gguf

PARAMETER temperature 0.7
PARAMETER num_ctx 8192
PARAMETER repeat_penalty 1.1

SYSTEM "Tu réponds toujours en français."
```

Puis :

```bash
ollama create gemma-local -f Modelfile
```

(Refais `create` à chaque modif du Modelfile.)

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## Étape 7 — Cheatsheet des commandes utiles

<details>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


| Action | Commande |
|--------|----------|
| Lister les modèles installés | `ollama list` |
| Voir les infos d'un modèle | `ollama show gemma-local` |
| Voir le Modelfile actuel | `ollama show gemma-local --modelfile` |
| Voir les paramètres | `ollama show gemma-local --parameters` |
| Copier/aliaser un modèle | `ollama cp gemma-local gemma-test` |
| Supprimer un modèle | `ollama rm gemma-test` |
| Stopper un modèle en mémoire | `ollama stop gemma-local` |
| Voir ce qui tourne en VRAM | `ollama ps` |
| Tuer Ollama | `pkill ollama` |
| Redémarrer le service | `sudo systemctl restart ollama` |
| Voir les logs | `journalctl -u ollama -f` |

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## Étape 8 — Dans le chat Ollama (commandes slash)

<details>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


Une fois `ollama run gemma-local` lancé :

| Commande | Effet |
|----------|-------|
| `/?` | Aide |
| `/set system "..."` | Changer le prompt système à la volée |
| `/set parameter temperature 0.5` | Changer un paramètre |
| `/show info` | Infos du modèle |
| `/clear` | Effacer la conversation |
| `/bye` | Sortir |

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## Étape 9 — Tester l'API depuis un autre programme

<details>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


Ollama expose une API compatible OpenAI sur `http://127.0.0.1:11434/v1`.

```bash
curl http://127.0.0.1:11434/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{"model":"gemma-local","messages":[{"role":"user","content":"dis bonjour"}]}'
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

## Récap : le flux complet en une page

<details>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


```
1. Installer Ollama          → curl -fsSL https://ollama.com/install.sh | sh
2. Vérifier le serveur       → systemctl status ollama && curl 127.0.0.1:11434/api/tags

Modèle standard (lib Ollama) :
3a. ollama pull qwen2.5:7b
3b. ollama run qwen2.5:7b

Modèle custom (HuggingFace) :
3a. Télécharger le .gguf dans ~/models/<nom>/
3b. echo "FROM ./mon-modele.gguf" > Modelfile
3c. ollama create mon-modele -f Modelfile
3d. ollama run mon-modele
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

## Troubleshooting

<details>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


| Erreur | Cause | Fix |
|--------|-------|-----|
| `Error: pull model manifest: file does not exist` | Tu pulls un modèle HuggingFace custom | Va à l'**étape 5** (Modelfile + create) |
| `'FROM' n'est pas reconnu` | Tu tapes `FROM ./...` dans le terminal | C'est la syntaxe **dans Modelfile**, pas une commande shell |
| `ECONNREFUSED 11434` | Service Ollama pas lancé | `sudo systemctl start ollama` |
| `Permission denied` sur `/usr/local/bin/ollama` | Install corrompue | Relance le script d'install |
| `unknown GGUF file format` | Téléchargement corrompu | Re-télécharge le `.gguf` |
| Pas de GPU détecté | Driver NVIDIA pas installé | `nvidia-smi` doit répondre. Sinon : `sudo apt install nvidia-driver-535` (Ubuntu/Debian) |
| Réponse très lente | Tourne en CPU | Vérifie `nvidia-smi` pendant `ollama run`. Si pas de processus, voir ligne précédente |

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


- [`02-[OPENCLAUDE-GRATUIT]-installation-ollama-lmstudio.md`](./02-[OPENCLAUDE-GRATUIT]-installation-ollama-lmstudio.md) — installer OpenClaude et le brancher sur Ollama
- [`03-[OPENROUTER-GRATUIT]-openclaude-modeles-cloud-gratuits.md`](./03-[OPENROUTER-GRATUIT]-openclaude-modeles-cloud-gratuits.md) — alternative cloud gratuite (sans GPU)
- [`04-[LLAMACPP-GRATUIT]-compilation-gguf-avance.md`](./04-[LLAMACPP-GRATUIT]-compilation-gguf-avance.md) — alternative avancée à Ollama
- [Bibliothèque officielle Ollama](https://ollama.com/library)

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>
