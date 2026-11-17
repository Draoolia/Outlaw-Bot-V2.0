# Outlaw A.I. — Gaming Outlaws Bot

Bot Discord **multifonction** (discord.js v14) conçu pour la communauté **Gaming Outlaws**.

Modération, sécurité, XP, économie, casino, musique, IA multi-providers, multilingue (8 langues), giveaways, annonces, et bien plus — le tout configurable via un panneau `/settings` (Components V2).

---

## Table des matières

1. [Prérequis & installation](#prérequis--installation)
2. [Configuration (`.env`)](#configuration-env)
3. [Démarrage](#démarrage)
4. [Fonctionnalités](#fonctionnalités)
   - [Aide & navigation](#aide--navigation)
   - [Intelligence artificielle](#intelligence-artificielle)
   - [Modération & staff](#modération--staff)
   - [Sécurité & anti-raid](#sécurité--anti-raid)
   - [Configuration serveur](#configuration-serveur)
   - [XP, niveaux & rang](#xp-niveaux--rang)
   - [Économie & casino](#économie--casino)
   - [Jeux](#jeux)
   - [Musique](#musique)
   - [Communauté](#communauté)
   - [Contenu & utilitaires](#contenu--utilitaires)
   - [Vocal & salons temporaires](#vocal--salons-temporaires)
   - [Premium / Freemium](#premium--freemium)
5. [Langues supportées](#langues-supportées)
6. [Architecture technique](#architecture-technique)
7. [Optimisations RAM](#optimisations-ram)
8. [Déploiement & maintenance](#déploiement--maintenance)
9. [Structure des permissions](#structure-des-permissions)
10. [Licence & crédits](#licence--crédits)

---

## Prérequis & installation

- **Node.js** ≥ 18
- Compte Discord Developer (application + bot)
- Accès à un hébergeur Linux recommandé (ex. Bot-Hosting)

```bash
npm install
cp .env.example .env   # puis renseigner les tokens
```

### Dépendances notables

| Package | Rôle |
|---------|------|
| `discord.js` ^14 | API Discord |
| `@discordjs/voice` + `@snazzah/davey` + `tweetnacl` + `opusscript` | Vocal / DAVE |
| `ffmpeg-static` + `play-dl` | Musique |
| `better-sqlite3` | Base SQLite locale |
| `@google/generative-ai` | Gemini |
| `@napi-rs/canvas` / `canvacard` / `jimp` | Images (welcome, casino, rank) |
| `discord-gamecord` | Mini-jeux |
| `node-cron` | Tâches planifiées (free games, reddit, giveaways…) |

---

## Configuration (`.env`)

| Variable | Obligatoire | Description |
|----------|-------------|-------------|
| `DISCORD_TOKEN` | Oui | Token du bot |
| `CLIENT_ID` | Oui | ID de l’application |
| `GUILD_ID` | Non | Serveur de test (déploiement rapide des commandes) |
| `OWNER_GUILD_ID` | Non | Serveur privé du créateur — **seul endroit où `/premium` est enregistrée** |
| `BOT_OWNER_ID` | Non | ID Discord du créateur (défaut interne si absent) |
| `GEMINI_API_KEY` | Non | Clé Google Gemini |
| `GROQ_API_KEY` / `OPENAI_API_KEY` / `ANTHROPIC_API_KEY` / `XAI_API_KEY` | Non | Clés du hub Multi-IA |
| `PREMIUM_SKU_ID` | Non | SKU Discord Premium (si monétisation store) |
| `PREFIX` | Non | Préfixe texte (défaut `!`) |

---

## Démarrage

```bash
node deploy-commands.js   # enregistre / met à jour les slash commands
node index.js             # lance le bot
```

---

## Fonctionnalités

### Aide & navigation

| Commande | Description |
|----------|-------------|
| `/help` | Panneau d’aide **Components V2** : catégories filtrées par rang (membre / modo / admin), langue du membre ou du serveur, couleur d’accent `/settings`. La commande `/premium` n’y apparaît **jamais**. |

---

### Intelligence artificielle

| Commande / flux | Description |
|-----------------|-------------|
| `/ai` | Hub Multi-IA (selon config serveur) |
| `/outlawchat` | Discussion avec **Outlaw A.I.** en messages privés |
| Mention `@bot` | Conversation contextuelle dans un salon |
| `/annonce` | Demander à l’IA de rédiger et poster un message dans un salon |
| `/set ai` | Clés API, quotas, rôles autorisés, providers |

**Providers supportés** (via `utils/aiRouter.js`) : Groq, ChatGPT (OpenAI), Gemini, xAI Grok, Claude.

- Historique de conversation par utilisateur  
- Quotas / coûts journaliers trackés en base  
- System prompt orienté communauté Gaming Outlaws (émulation, optimisation, homebrew, etc.)  
- Recherche web optionnelle (`utils/webSearch.js`)

---

### Modération & staff

| Commande | Description |
|----------|-------------|
| `/moderation` (ou flux `/mod`) | Menu interactif : mute, unmute, warn, kick, ban, prison |
| `/clearmessages` | Suppression en masse (1–100), filtre optionnel par utilisateur |
| **Prison / Jail** | Rôle + salons configurables, retour / application depuis le panneau modo |
| **Warns** | Stockés en base (`warnings`) |

Permissions : Administrateur Discord, rôles nommés (`Admin`, `Modérateur`, `Moderator`, `Mod`), ou permissions Kick / Ban / ModerateMembers.

---

### Sécurité & anti-raid

| Élément | Description |
|---------|-------------|
| `/protect` | Point d’entrée protection (raid, captcha, DEFCON, logs, prison) |
| **Anti-raid** | Seuils, durée, actions (kick…), fermeture des DMs optionnelle |
| **Captcha V2** | À l’arrivée + interactions éphémères (`utils/captcha.js`, events dédiés) |
| **DEFCON** | Niveau de protection affiché / publié |
| **Honeypot** | Salon piège |
| **Image antispam / antiscam** | Analyse d’images, boutons libérer / bannir depuis les logs |
| **Logs serveur** | Membres, messages, salons, vocal, emojis — formats compact / encadré / embed, couleur d’accent |

---

### Configuration serveur

| Commande | Description |
|----------|-------------|
| `/settings` | **Panneau central** (Components V2) : toutes les fonctions admin |
| `/set` | Réglages avancés (ex. hub IA) |
| `/roles` | Gestion de rôles via menus |
| **Panneaux setup (`srv*`)** | Accueil, langue, rôles, raid, prison, welcome, levels, AI, tickets, logs, captcha, économie, reddit, free games, vocal… |

**Couleur d’accent** : définie dans `/settings`  
- Plan **gratuit** → défaut `#AC17E3`  
- Plan **Premium** → couleur personnalisée possible  

Utilisée par `/help`, free games, Reddit, logs (défaut), embeds UI, etc.

---

### XP, niveaux & rang

| Commande | Description |
|----------|-------------|
| `/level` | Carte / infos de niveau |
| `/rank` | Classement XP du serveur (paginé) |
| `/xp` (admin) | Configuration du système XP |

- Gain d’XP à l’activité, multiplieurs (limites freemium / premium)  
- Rôles de niveau automatiques  
- Config via panneau Levels dans `/settings`

---

### Économie & casino

| Commande | Description |
|----------|-------------|
| `/eco` | Solde, daily, work, beg, pay, rob, boutique de rôles, classement |
| `/casino` | Blackjack, roulette, slots, coinflip, dice, hi-lo + **tables multijoueur** |

- Wallet en base SQLite (lié à l'activité XP et utilisable au casino, avec mode jetons gratuits autonome si l'économie est désactivée)  
- Rendu Canvas à la volée (`casinoRenderer.js`) — **aucun cache d’images en RAM**  
- Sessions de tables expirées après 5 min d’inactivité  

---

### Jeux

| Commande | Description |
|----------|-------------|
| `/connect4` | Puissance 4 |
| `/tictactoe` | Morpion |
| `/rps` | Pierre-feuille-ciseaux |
| `/slots` | Machine à sous |
| `/snake` | Snake |
| `/findemoji` | Trouver l’emoji |

Parties temporaires en mémoire, **TTL 5 minutes** pour éviter les fuites RAM.

---

### Musique

| Commande | Description |
|----------|-------------|
| `/music play` | Recherche / lien YouTube ou SoundCloud |
| `/music pause` / `resume` / `skip` / `stop` | Contrôle de lecture |
| `/music queue` / `nowplaying` / `volume` / `loop` | File, volume, boucle |

**Panneau lecteur** (Components V2 + boutons) : Pause/Reprendre, Suivant, Boucle, Mélanger, Stop.

Particularités techniques :
- `FFMPEG_PATH` via `ffmpeg-static` (Linux / Bot-Hosting)
- Chiffrement vocal DAVE (`@snazzah/davey`, `tweetnacl`, `opusscript`)
- **Fallback automatique YouTube → SoundCloud** si blocage anti-bot (403, “Sign in to confirm”…)
- Max **3 streams** simultanés sur tout le bot (éco-RAM)
- Session vocale détruite après **2 min** d’inactivité
- `inlineVolume: false` pour éviter le décodage PCM lourd

---

### Communauté

| Commande / système | Description |
|--------------------|-------------|
| `/birthday` | Définir, consulter ou retirer son anniversaire (`set`, `show`, `remove`, `upcoming` — salon d'annonce configurable uniquement par l'admin dans `/settings`) |
| `/giveaway` | Création, participants, tirage (niveau XP minimum anti-leech configurable dans `/settings`) |
| **Welcome / Goodbye** | Messages + cartes image, config par serveur |
| **Règlement** | Acceptation par bouton + rôle, traduction par langue |
| **Reaction roles** | Emoji (`/reactionrole`) et boutons (`/reactionrolebutton`) |
| **Counting** | Jeu du compteur dans un salon dédié |
| **Tickets** | Ouverture / fermeture, transcript HTML, archives |

---

### Contenu & utilitaires

| Commande / flux | Description |
|-----------------|-------------|
| `/embed` | Constructeur d’embeds (`create` / `edit`, upload direct depuis la galerie photo en local sans hébergement externe, MP, V2) |
| `/translate` | Traduire texte ou message |
| `/addtranslate` / `/addlangmenu` | Bouton ou menu de langues sous un message |
| `/meme` | Mèmes |
| `/joke` | Blagues |
| `/youtube watch` | YouTube Watch Together en salon vocal |
| **YouTube notifs** | Suivi de chaînes → annonces de nouvelles vidéos |
| **Free games** | Annonces de jeux gratuits (GamerPower), plateformes filtrables, couleur d’accent serveur |
| **Reddit** | Sources par subreddit / mots-clés → posts dans un salon, couleur d’accent serveur |

Tâches planifiées (`node-cron`) pour free games, Reddit, fin de giveaways, etc.

---

### Vocal & salons temporaires

| Élément | Description |
|---------|-------------|
| **Temp voice** | Salons vocaux temporaires (limites freemium / premium) |
| `voiceStateUpdate` / `voiceMenus` | Gestion join/leave et menus vocaux |

---

### Premium / Freemium

| Commande | Accès | Description |
|----------|--------|-------------|
| `/premium` | **Créateur uniquement** (`BOT_OWNER_ID`) | Gestion des licences serveurs (`grant`, `revoke`, `status`, `servers`, réinitialisation d'essai) |

- Enregistrée **uniquement** sur `OWNER_GUILD_ID` (jamais en global, absente de `/help`)
- Essai gratuit de 7 jours automatique à l’arrivée sur un nouveau serveur
- Limites différenciées (extraits) :

| Fonction | Gratuit | Premium |
|----------|---------|---------|
| Free games (plateformes) | 1 | Illimité |
| Temp voice | 2 | 25 |
| Reddit sources | 5 | 25 |
| YouTube chaînes | 3 | 15 |
| IA payante | 1 | 4 |
| Reaction roles / msg | 5 | 25 |
| Boutique économie (`/eco shop`) | 3 articles | 25 articles |
| Giveaways | 1 | 5 |
| Multiplicateur XP max | 1.5 | 10 |
| Accent / fond welcome custom | Non | Oui |
| Sécurité avancée (DEFCON, Honeypot, Captcha, logs avancés) | Non | Oui |

---

## Langues supportées

| Code | Langue |
|------|--------|
| `fr` | Français |
| `en` | English |
| `es` | Español |
| `de` | Deutsch |
| `it` | Italiano |
| `pt` | Português |
| `ru` | Русский |
| `el` | Ελληνικά |

- Langue **serveur** + mode **multilingue** (rôles langue par membre)
- `getUserLanguage(member, guildId)` centralise le choix (rôle → serveur → défaut)
- `/help`, panneaux `/settings`, traductions de messages/embeds

---

## Architecture technique

```
index.js                 → Client, cron, events globaux, routing léger
handlers/
  interactionRouter.js   → Tous les boutons / menus / modales / slash
commands/
  admin/  games/  public/  utility/
events/                  → Captcha, logs, raid, honeypot, vocal…
utils/
  database.js            → SQLite (better-sqlite3) + migrations
  setup.js               → Panneau /settings (très complet)
  aiRouter.js            → Hub multi-IA
  musicManager.js        → Lecteur vocal
  casinoRenderer.js      → Génération graphique PNG Casino & Leaderboard
  captcha.js, logs.js, premium.js, xp.js, …
  v2.js                  → Rendu Components V2 + accent couleur
locales/                 → fr, en, es, de, it, pt, ru, el
data/bot.db              → Base locale
assets/                  → Bannières UI (help, moderation, xp…)
```

**Base de données** (principales tables) :  
`birthdays`, `warnings`, `mutes`, `reaction_roles`, `button_roles`, `youtube_*`, `giveaways`, `gemini_history`, `welcome_config`, `freegames_*`, `active_jails`, `ai_daily_usage`, `ai_usage_costs`, `user_xp`, `xp_config`, `level_roles`, `voice_config`, `temp_channels`, `raid_config`, `counting`, `guild_premium`, `guild_trials`, `economy_*`, `music_settings`, `bot_settings`, …

---

## Optimisations RAM

Pensé pour tourner sur **plusieurs dizaines de serveurs** sans explosion mémoire :

1. **Caches Discord.js limités** + sweepers messages / users  
2. **Musique** : max 3 streams simultanés, `inlineVolume: false`, destruction session après 2 min d’inactivité  
3. **Sessions temporaires** (embed builder, tables casino, mini-jeux) : **TTL 5 min**  
4. **Casino / cartes / embeds** : Canvas généré à la volée et pièces jointes locales, **aucun buffer global ni stockage d'images utilisateurs**

---

## Déploiement & maintenance

### Enregistrement des commandes slash

```bash
node deploy-commands.js
```

- Si `GUILD_ID` est défini → déploiement **guild** (rapide, tests)
- Sinon → déploiement **global** (propagation jusqu’à ~1 h)
- `/premium` est **exclue** du global / guild de test et poussée **uniquement** sur `OWNER_GUILD_ID`

### Sauvegarde de la base de données

- Avant toute mise à jour majeure ou migration de structure sur l'hébergeur (**Bot-Hosting**), télécharge une copie de sécurité du fichier **`data/bot.db`** (ainsi que `data/bot.db-wal` / `data/bot.db-shm` s'ils sont présents) afin de préserver les soldes économie, niveaux XP, configurations `/settings` et licences Premium des serveurs.

---

## Structure des permissions

| Rang `/help` | Voit |
|--------------|------|
| **member** | Accueil, IA, jeux, social, utils |
| **mod** | + modération / staff |
| **admin** | + administration /settings, protect, set, xp, roles… (**sauf** `/premium`) |

`/premium` : uniquement le créateur du bot, sur le serveur propriétaire (`OWNER_GUILD_ID`).

---

## Licence & crédits

- Projet **Gaming Outlaws** — bot **Outlaw A.I.**
- Stack open-source listée dans `package.json`
- Aucune garantie ; usage à vos risques sur votre infrastructure

---

*README généré pour le bot Gaming Outlaws / Outlaw A.I. — fonctionnalités à jour.*
