# 🏗️ architecture-laravel — un skill Claude Code pour démarrer un projet Laravel sur de bonnes bases

> **Fini le code rangé « au hasard » à chaque prompt.**
> Ce skill installe dans ton projet Laravel une **architecture en couches** claire, inspirée de Spring Boot, et un fichier `CLAUDE.md` qui oblige l'IA à la respecter ensuite.

Créé par **Mr.Delafric** · Dev · DevOps · Cybersécurité & IA · TikTok [@mrdelafric](https://www.tiktok.com/@mrdelafric)

🇬🇧 *English summary at the bottom.*

---

## Pourquoi ce skill ?

Quand on code avec l'IA sans plan, chaque nouvelle fonctionnalité est rangée différemment : une requête SQL dans un contrôleur ici, de la validation dans une vue là… Au bout de quelques semaines, le projet devient impossible à faire évoluer.

L'IA est un excellent maçon. **Ce skill lui donne le plan de la maison.**

## L'architecture

```
Requête HTTP
   │
   ▼
FormRequest   → toute la validation
   │
   ▼
Controller    → reçoit la requête, appelle UN service, renvoie la réponse
   │
   ▼
Service       → la logique métier (les règles de ton application)
   │
   ▼
Repository    → le SEUL endroit qui parle à la base de données (Eloquent)
   │
   ▼
Model → Base de données
```

Chaque couche a **un seul rôle**. Résultat : on sait toujours où ranger le code, on teste plus facilement, et l'IA ne mélange plus tout.

| Laravel (ce skill) | Équivalent Spring Boot |
|---|---|
| `FormRequest` | `@Valid` + DTO de requête |
| `Controller` | `@RestController` / `@Controller` |
| `Service` + interface | `@Service` |
| `Repository` + `BaseRepository` | `JpaRepository` |
| `RepositoryServiceProvider` | Injection de dépendances de Spring |

## Ce que fait le skill

1. **Vérifie ton environnement** : PHP, Composer, Node, version de Laravel, Breeze, Tailwind.
2. **Te pose 12 questions d'un coup**, avec des valeurs par défaut :

   | # | Question | Choix |
   |---|---|---|
   | 1 | Nom du projet | libre |
   | 2 | API | oui / non (Sanctum) |
   | 3 | Langue | français / anglais / bilingue |
   | 4 | Thème clair/sombre | oui / non |
   | 5 | Style du site public | Tailwind / daisyUI / Bootstrap / CSS pur |
   | 6 | Base de données | SQLite / MySQL |
   | 7 | Hébergement | OVH mutualisé / VPS / autre |
   | 8 | Gestion des accès | admin simple / rôles / permissions / rôles + permissions |
   | 9 | Packs de paquets | vitrine (SEO, sitemap, images, paramètres…) / gestion (PDF, Excel, logs, sauvegardes) |
   | 10 | Git + premier commit | oui / non |
   | 11 | Extras | outils de dev (Debugbar, Larastan…), config VS Code |
   | 12 | Fuseau horaire | Africa/Abidjan par défaut |

3. **Te montre un récapitulatif et attend ton « oui »** avant de toucher à quoi que ce soit.
4. **Installe et crée** : Breeze (Blade), l'arborescence complète, `BaseRepository`, `RepositoryServiceProvider`, les routes admin séparées, le site public avec son propre layout, les pages d'erreur, le thème clair/sombre, les rôles et permissions, les paquets choisis.
5. **Génère `CLAUDE.md`** (les règles pour l'IA) et **`docs/ARCHITECTURE.md`** (la même chose pour les humains), plus **`docs/DEPLOIEMENT.md`**, une checklist de mise en ligne.
6. **Vérifie que tout marche** : migrations, build, tests, formatage (Pint), puis premier commit.

### Règles de sécurité intégrées
- ❌ Jamais de mot de passe en dur : le compte admin est lu dans `.env` (`ADMIN_NAME`, `ADMIN_EMAIL`, `ADMIN_PASSWORD`).
- ❌ Ne jamais écraser ni supprimer un fichier existant sans te le dire ou te le demander.
- ✅ Vérifie la compatibilité de chaque paquet avec ta version de Laravel avant de l'installer.

## L'arborescence générée (extrait)

```
app/
├── Actions/  DTOs/  Enums/  Events/  Exceptions/  Jobs/  Policies/  Traits/
├── Http/
│   ├── Controllers/{Web, Admin, Api}
│   └── Requests/{Web, Admin, Api}
├── Repositories/
│   ├── Contracts/BaseRepositoryInterface.php
│   └── BaseRepository.php
├── Services/Contracts/
└── Providers/RepositoryServiceProvider.php
docs/ARCHITECTURE.md · docs/DEPLOIEMENT.md
resources/views/{layouts, components/{web,admin,ui}, errors, web, admin, emails}
routes/admin.php        ← back-office : préfixe /admin, protégé
CLAUDE.md               ← les règles que l'IA doit suivre
```

## Installation

**Prérequis :** [Claude Code](https://code.claude.com/docs/en/overview), PHP, Composer, Node.js, et un projet Laravel déjà créé (`laravel new mon-projet`). Le skill vise Laravel 11 et plus récent.

### Option A — pour tous tes projets (recommandé)
```bash
git clone https://github.com/DELAFRIC/laravel-architecture-skill.git
mkdir -p ~/.claude/skills
cp -r laravel-architecture-skill/architecture-laravel ~/.claude/skills/
```

### Option B — pour un seul projet
Dans le dossier de ton projet Laravel :
```bash
mkdir -p .claude/skills
cp -r /chemin/vers/laravel-architecture-skill/architecture-laravel .claude/skills/
```
Si tu commites `.claude/skills/`, toute ton équipe en profite.

## Utilisation

Dans ton projet Laravel, lance Claude Code puis tape :
```
/architecture-laravel
```
Tu peux aussi pré-remplir des réponses :
```
/architecture-laravel 1 MaBoutique, 3 bilingue, 6 MySQL, 8 d
```

Ensuite, pour ajouter une fonctionnalité (par exemple des « Produits »), demande simplement à Claude :
> « Ajoute une ressource Produit en respectant l'architecture du CLAUDE.md. »

Il suivra la checklist : modèle, Repository, Service, FormRequests, Controller, vues, Policy et tests.

## FAQ

**« Eloquent est déjà un repository, pourquoi en rajouter un ? »**
C'est un choix assumé. Pour un tout petit site, ce n'est pas indispensable. Mais dès que le projet grandit ou que vous êtes plusieurs :
- la logique métier reste dans les Services, facile à tester et à réutiliser ;
- l'accès aux données est centralisé à un seul endroit ;
- les développeurs qui viennent de Java/Spring s'y retrouvent immédiatement ;
- et surtout, l'IA a des règles claires et ne range plus le code au hasard.

**Ça marche sans Claude Code ?**
Le skill est fait pour Claude Code, mais `docs/ARCHITECTURE.md` décrit l'architecture pour n'importe quel développeur.

**Pourquoi Breeze et pas Filament ?**
Pour garder un back-office en Blade, simple à comprendre et à personnaliser, sans dépendre d'un gros paquet.

## Contribuer

Une idée, un bug, une option à ajouter ? Ouvre une *issue* ou une *pull request*. Et si le skill t'aide, mets une ⭐ au dépôt, ça m'aide à le faire connaître.

## Licence

[MIT](LICENSE) : tu peux l'utiliser, le modifier et le partager librement, en gardant la mention de l'auteur.

---

## 🇬🇧 English summary

**architecture-laravel** is a [Claude Code](https://code.claude.com/docs/en/overview) skill that sets up a **layered architecture** in an existing Laravel project (Breeze/Blade): FormRequest → Controller → Service → Repository → Model, inspired by Spring Boot. It asks 12 questions (API, language, dark mode, public site style, database, hosting, access control, package packs, Git, extras, time zone), shows a summary and waits for your approval, then installs everything, generates a `CLAUDE.md` so the AI keeps following the architecture, and runs migrations, build, tests and Pint.

**Install:** copy the `architecture-laravel/` folder into `~/.claude/skills/` (all projects) or `.claude/skills/` (one project). **Use:** run `/architecture-laravel` inside your Laravel project. The skill's instructions and generated files are in French. License: MIT.
