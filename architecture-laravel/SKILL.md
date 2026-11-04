---
name: "architecture-laravel"
description: "Met en place une architecture Laravel + Breeze en couches (Controller → Service → Repository), inspirée de Spring Boot, via un questionnaire. Pour /architecture-laravel ou structurer un nouveau projet Laravel."
license: MIT
---

# /architecture-laravel

Tu mets en place cette architecture dans un projet Laravel **existant** (créé avec `laravel new` ou `composer create-project`).
L'architecture est volontairement en couches et inspirée de Spring Boot (Controller → Service → Repository) : on retrouve la même logique d'un framework à l'autre, et l'IA comme les humains savent toujours où ranger chaque morceau de code.

Langue de travail : **français**. Explique brièvement ce que tu fais à chaque étape (l'utilisateur apprend).

Arguments éventuels : `$ARGUMENTS` peut contenir des réponses déjà données (ex. `1 MonProjet, 3 bilingue, 8 d`). Utilise-les pour pré-remplir le questionnaire.

---

## Règles absolues

1. **Ne jamais écraser un fichier existant.** Si un fichier à créer existe déjà : le lire, et soit le compléter (ajout ciblé), soit le laisser et le signaler dans le résumé final.
2. **Ne jamais supprimer de fichier** sans demander.
3. **`.gitkeep` uniquement dans les dossiers vides**, ajouté à la toute fin. Jamais dans `vendor/`, `node_modules/`, `storage/`, `bootstrap/cache/`.
4. **Ne jamais écrire de mot de passe en dur.** Le compte admin initial lit `ADMIN_NAME`, `ADMIN_EMAIL`, `ADMIN_PASSWORD` dans `.env`.
5. Avant d'installer un paquet Composer/npm : vérifier qu'il est compatible avec la version de Laravel installée (`composer require` échoue sinon). En cas d'échec : ne pas forcer, prévenir l'utilisateur, proposer une alternative, continuer le reste.
6. L'admin est **toujours** Breeze (stack Blade) + back-office Blade fait main. Pas de Filament.

---

## Étape 1 — Vérifications (sans poser de question)

Exécute et contrôle :

- `php -v`, `composer -V`, `node -v`, `npm -v` → tous présents, sinon s'arrêter et expliquer quoi installer.
- `composer.json` contient `laravel/framework` → sinon s'arrêter : « Ce dossier n'est pas un projet Laravel ».
- `php artisan --version` → noter la version (les instructions ci-dessous visent Laravel 11 et supérieur).
- Breeze installé ? (`laravel/breeze` dans `composer.json` **ou** présence de `routes/auth.php` + `app/Http/Controllers/Auth/`).
- Architecture déjà générée ? (présence de `app/Repositories/` ou d'un `CLAUDE.md` mentionnant `/architecture-laravel`) → si oui, prévenir et demander s'il faut seulement compléter ce qui manque.
- Version de Tailwind : `tailwind.config.js` présent = Tailwind 3 ; `@import "tailwindcss"` dans `resources/css/app.css` sans config = Tailwind 4. À re-vérifier après l'installation de Breeze.

---

## Étape 2 — Questionnaire (une seule fois, tout d'un coup)

Affiche exactement ce bloc (valeurs par défaut entre crochets), puis attends la réponse :

```
Configuration du projet — répondez « ok » pour tout garder,
ou indiquez seulement ce qui change (ex. « 1 MonProjet, 3 bilingue, 8 d »).

1.  Nom du projet        : [Mon Projet]
2.  API                  : [non]             (oui / non)
3.  Langue               : [français]        (français / anglais / bilingue)
4.  Thème clair/sombre   : [oui]             (oui / non)
5.  Style du site public : [tailwind]        (tailwind / daisyui / bootstrap / css)
6.  Base de données      : [SQLite]          (SQLite / MySQL)
7.  Hébergement          : [OVH mutualisé]   (OVH mutualisé / VPS / autre)
8.  Gestion des accès    : [a]               (a admin simple / b rôles / c permissions / d rôles + permissions)
9.  Packs de paquets     : [vitrine]         (aucun / vitrine / gestion / les deux / à la carte)
10. Git + 1er commit     : [oui]             (oui / non)
11. Extras               : [aucun]           (aucun / dev / vscode / dev + vscode)
12. Fuseau horaire       : [Africa/Abidjan]  (tout fuseau valide, ex. Europe/Paris, America/Montreal)
```

Si l'utilisateur demande ce qu'une option veut dire, explique-la simplement (voir les sections « Options » plus bas) puis repose la question.
Si la réponse 9 est « à la carte », affiche la liste des paquets des deux packs et laisse-le cocher.
Si la réponse 8 est b, c ou d : demande aussi la liste initiale des rôles et/ou des ressources (ex. « admin, editeur » et « produits, services, realisations »). Valeurs par défaut : rôles `admin, editeur` ; ressources `utilisateurs`.

Ensuite, affiche un **récapitulatif** de ce qui va être fait (paquets installés, fichiers clés créés, commandes lancées) et demande « Je lance ? ». N'exécute rien avant le « oui ».

---

## Étape 3 — Installation de base

1. **Breeze** (si absent) :
   ```
   composer require laravel/breeze --dev
   php artisan breeze:install blade
   ```
   Si l'option 4 = oui, ajouter `--dark`. Si l'installateur pose des questions (tests Pest/PHPUnit), demander à l'utilisateur.
2. **.env** (et `.env.example` avec des valeurs vides pour les secrets) :
   - `APP_NAME="<Nom du projet>"`
   - Langue : voir Option 3.
   - Base : `DB_CONNECTION=sqlite` (vérifier que `database/database.sqlite` existe, sinon le créer) **ou** `DB_CONNECTION=mysql` + `DB_HOST/DB_PORT/DB_DATABASE/DB_USERNAME/DB_PASSWORD` (laisser à l'utilisateur les valeurs).
   - Ajouter `ADMIN_NAME=`, `ADMIN_EMAIL=`, `ADMIN_PASSWORD=` (vides dans `.env.example`, demander les valeurs à l'utilisateur pour `.env`).
3. **Fuseau horaire** (option 12, `Africa/Abidjan` par défaut) : vérifier que c'est un fuseau valide (`php -r "new DateTimeZone('<fuseau>');"`). Si `config/app.php` lit `env('APP_TIMEZONE', ...)`, mettre `APP_TIMEZONE=<fuseau>` dans `.env` ; sinon modifier directement la valeur `'timezone'` dans `config/app.php`.
4. **API** (option 2 = oui) : `php artisan install:api` (installe Sanctum + `routes/api.php`).

---

## Étape 4 — Arborescence

Crée ces dossiers (les fichiers marqués sont créés à l'étape 5) :

```
app/
├── Actions/
├── Console/Commands/
├── DTOs/
├── Enums/
├── Events/
├── Exceptions/                      ← exceptions métier uniquement (PAS de Handler.php : Laravel 11+ gère les erreurs dans bootstrap/app.php)
├── Http/
│   ├── Controllers/
│   │   ├── Web/                     ← site public
│   │   ├── Admin/                   ← back-office   (+ DashboardController.php)
│   │   └── Api/                     ← si API
│   ├── Middleware/
│   ├── Requests/
│   │   ├── Web/
│   │   ├── Admin/
│   │   └── Api/                     ← si API
│   └── Resources/                   ← si API
├── Jobs/
├── Listeners/
├── Mail/
├── Notifications/
├── Policies/
├── Providers/                       (+ RepositoryServiceProvider.php)
├── Repositories/
│   └── Contracts/                   (+ BaseRepositoryInterface.php ; Repositories/BaseRepository.php)
├── Services/
│   └── Contracts/
├── Traits/
└── View/
    └── Composers/
docs/                                (+ ARCHITECTURE.md ; DEPLOIEMENT.md)
lang/
resources/
├── css/                             (+ web.css ; tokens.css)
├── js/                              (+ web.js ; theme.js si thème)
└── views/
    ├── layouts/                     (+ web.blade.php)
    ├── components/
    │   ├── web/                     (+ header.blade.php, footer.blade.php)
    │   ├── admin/
    │   └── ui/                      (+ theme-toggle / language-switcher selon options)
    ├── errors/                      (+ 403, 404, 419, 500, 503 .blade.php)  ← DOIT être ici, pas dans web/
    ├── web/
    │   ├── home/                    (+ index.blade.php)
    │   ├── pages/
    │   └── partials/
    ├── admin/
    │   ├── dashboard/               (+ index.blade.php)
    │   ├── parametres/
    │   └── partials/
    └── emails/
routes/                              (+ admin.php)
tests/
├── Feature/
│   ├── Web/
│   └── Admin/
└── Unit/
    ├── Services/
    └── Repositories/
```

Ajoute selon les options : `app/Settings/` + `database/settings/` (laravel-settings), `resources/views/pdf/` (dompdf), `resources/views/admin/users/` et `admin/roles/` (accès b/c/d).

---

## Étape 5 — Fichiers à créer

### 5.1 Repositories (équivalent de `JpaRepository`)

`app/Repositories/Contracts/BaseRepositoryInterface.php`
```php
<?php

namespace App\Repositories\Contracts;

use Illuminate\Contracts\Pagination\LengthAwarePaginator;
use Illuminate\Database\Eloquent\Collection;
use Illuminate\Database\Eloquent\Model;

interface BaseRepositoryInterface
{
    public function all(array $columns = ['*']): Collection;
    public function paginate(int $perPage = 15): LengthAwarePaginator;
    public function find(int|string $id): ?Model;
    public function findOrFail(int|string $id): Model;
    public function create(array $data): Model;
    public function update(int|string $id, array $data): Model;
    public function delete(int|string $id): bool;
}
```

`app/Repositories/BaseRepository.php`
```php
<?php

namespace App\Repositories;

use App\Repositories\Contracts\BaseRepositoryInterface;
use Illuminate\Contracts\Pagination\LengthAwarePaginator;
use Illuminate\Database\Eloquent\Collection;
use Illuminate\Database\Eloquent\Model;

abstract class BaseRepository implements BaseRepositoryInterface
{
    public function __construct(protected Model $model) {}

    public function all(array $columns = ['*']): Collection
    {
        return $this->model->newQuery()->get($columns);
    }

    public function paginate(int $perPage = 15): LengthAwarePaginator
    {
        return $this->model->newQuery()->latest()->paginate($perPage);
    }

    public function find(int|string $id): ?Model
    {
        return $this->model->newQuery()->find($id);
    }

    public function findOrFail(int|string $id): Model
    {
        return $this->model->newQuery()->findOrFail($id);
    }

    public function create(array $data): Model
    {
        return $this->model->newQuery()->create($data);
    }

    public function update(int|string $id, array $data): Model
    {
        $record = $this->findOrFail($id);
        $record->update($data);

        return $record->refresh();
    }

    public function delete(int|string $id): bool
    {
        return (bool) $this->findOrFail($id)->delete();
    }
}
```

### 5.2 RepositoryServiceProvider (équivalent de l'injection de Spring)

Crée-le avec `php artisan make:provider RepositoryServiceProvider` (Laravel 11+ l'ajoute automatiquement à `bootstrap/providers.php` — vérifier), puis remplace le contenu généré :

```php
<?php

namespace App\Providers;

use Illuminate\Support\ServiceProvider;

class RepositoryServiceProvider extends ServiceProvider
{
    /**
     * Interface => implémentation.
     * Ajouter une ligne pour chaque nouveau Repository et chaque nouveau Service.
     */
    public array $bindings = [
        // \App\Repositories\Contracts\ProduitRepositoryInterface::class => \App\Repositories\ProduitRepository::class,
        // \App\Services\Contracts\ProduitServiceInterface::class => \App\Services\ProduitService::class,
    ];
}
```

### 5.3 Routes admin

`routes/admin.php`
```php
<?php

use App\Http\Controllers\Admin\DashboardController;
use Illuminate\Support\Facades\Route;

// Préfixe /admin, noms admin.*, middleware web + auth + accès admin (voir bootstrap/app.php)
Route::get('/', DashboardController::class)->name('dashboard');
```

Dans `bootstrap/app.php`, dans `->withRouting(...)`, ajouter le paramètre `then:` (en gardant ce qui existe) :
```php
then: function () {
    Illuminate\Support\Facades\Route::middleware(['web', 'auth', /* MIDDLEWARE_ADMIN */])
        ->prefix('admin')
        ->name('admin.')
        ->group(base_path('routes/admin.php'));
},
```
`MIDDLEWARE_ADMIN` = `'admin'` (option a), `'role:admin'` (b ou d), `'permission:admin.acceder'` (c).

`app/Http/Controllers/Admin/DashboardController.php` : contrôleur invocable qui retourne `view('admin.dashboard.index')`.

**Dashboard Breeze** : déplacer le contenu de `resources/views/dashboard.blade.php` vers `resources/views/admin/dashboard/index.blade.php` (copier puis supprimer l'ancien **après accord**), et dans `routes/web.php` remplacer la route `dashboard` par une redirection en gardant son nom (Breeze redirige vers `route('dashboard')` après connexion) :
```php
Route::get('/dashboard', fn () => redirect()->route('admin.dashboard'))
    ->middleware(['auth'])
    ->name('dashboard');
```

### 5.4 Site public

- `app/Http/Controllers/Web/HomeController.php` (invocable → `view('web.home.index')`) et route `Route::get('/', HomeController::class)->name('home');` dans `routes/web.php` (remplace la route `welcome`).
- `resources/views/layouts/web.blade.php` : layout du site public avec `@vite(['resources/css/web.css', 'resources/js/web.js'])`, `<x-web.header />`, `{{ $slot }}` ou `@yield('content')`, `<x-web.footer />`, `lang="{{ str_replace('_', '-', app()->getLocale()) }}"`, et le script anti-flash si thème (Option 4).
- `components/web/header.blade.php` et `footer.blade.php` dans le style choisi (Option 5).
- `views/errors/` : 403, 404, 419, 500, 503 qui utilisent le layout web, textes dans la langue choisie.

### 5.5 Vite — deux entrées séparées

Dans `vite.config.js`, l'entrée `input` devient :
```js
input: [
    'resources/css/app.css', 'resources/js/app.js',   // admin (Breeze / Tailwind)
    'resources/css/web.css', 'resources/js/web.js',   // site public (style choisi)
],
```

### 5.6 CLAUDE.md (racine du projet)

Génère ce fichier en remplaçant les `<…>` par les réponses du questionnaire :

````markdown
# <Nom du projet> — Conventions pour Claude

Projet Laravel <version> + Breeze (Blade). Architecture générée par /architecture-laravel.

## Configuration
- Langue : <français | anglais | bilingue FR/EN> — Fuseau : <fuseau>
- Thème clair/sombre : <oui | non>
- Style du site public : <tailwind | daisyui | bootstrap | css> — Admin : Tailwind (Breeze)
- Base de données : <SQLite | MySQL> — Hébergement : <…>
- Gestion des accès : <a | b | c | d> — API : <oui | non>
- Paquets : <liste>

## Architecture en couches (même logique que Spring Boot)
Requête → FormRequest (validation) → Controller → Service → Repository → Model → base de données

- **Controller** : reçoit la requête, appelle UN service, retourne une vue / redirection / Resource. Ne touche JAMAIS au Model ni à Eloquent.
- **FormRequest** (`app/Http/Requests/{Web|Admin|Api}`) : toute la validation. Jamais de `$request->validate()` dans un controller.
- **Service** (`app/Services`, interface dans `Services/Contracts`) : logique métier. Ne parle qu'aux Repositories (et aux Actions / Events / Mail).
- **Repository** (`app/Repositories`, interface dans `Repositories/Contracts`) : SEUL endroit où Eloquent est utilisé. Hérite de `BaseRepository`.
- **DTO** (`app/DTOs`) : transporter des données structurées entre couches.
- **Enum** (`app/Enums`) : statuts, rôles, types — jamais de chaînes « magiques ».
- **Action** (`app/Actions`) : une classe = une opération ponctuelle (méthode `handle()` ou `__invoke()`).
- Toute nouvelle interface est liée à son implémentation dans `app/Providers/RepositoryServiceProvider.php` (`$bindings`).
- Injection par le constructeur (`public function __construct(private ProduitServiceInterface $produits) {}`).

## Ajouter une ressource (ex. « Produit ») — checklist
1. `php artisan make:model Produit -mf` (modèle + migration + factory)
2. `Repositories/Contracts/ProduitRepositoryInterface` (extends BaseRepositoryInterface) + `Repositories/ProduitRepository` (extends BaseRepository, constructeur `public function __construct(Produit $model) { parent::__construct($model); }`)
3. `Services/Contracts/ProduitServiceInterface` + `Services/ProduitService`
4. Bindings dans `RepositoryServiceProvider`
5. `Requests/Admin/StoreProduitRequest` + `UpdateProduitRequest`
6. `Controllers/Admin/ProduitController` (resource) + routes dans `routes/admin.php`
7. Vues `views/admin/produits/` : `index`, `create`, `edit`, `_form`
8. Côté public si besoin : `Controllers/Web/ProduitController` + `views/web/...`
9. `Policies/ProduitPolicy` <si accès b/c/d : permissions produits.voir|creer|modifier|supprimer dans PermissionEnum>
10. Tests : `tests/Feature/Admin/ProduitTest`, `tests/Unit/Services/ProduitServiceTest`

## Vues
- Site public : layout `layouts/web.blade.php`, vues dans `views/web/`, composants `<x-web.*>`.
- Admin : layout Breeze `layouts/app.blade.php` (`<x-app-layout>`), vues dans `views/admin/`, composants `<x-admin.*>`.
- Composants génériques : `<x-ui.*>`.
- Pages d'erreur : `views/errors/` uniquement.
- CSS/JS : admin → `app.css`/`app.js` ; public → `web.css`/`web.js`. Ne jamais mélanger.
<si bilingue : - Tous les textes visibles passent par `__('...')`. Contenu en base traduisible via spatie/laravel-translatable.>
<si thème : - Couleurs uniquement via les variables de `resources/css/tokens.css`. Tester chaque écran en clair ET en sombre.>

## Routes
- `routes/web.php` : site public (noms simples : `home`, `contact`…)
- `routes/admin.php` : back-office (préfixe `/admin`, noms `admin.*`, protégé)
- `routes/auth.php` : Breeze — ne pas modifier sans raison
<si API : - `routes/api.php` : API (Sanctum), réponses via `Http/Resources`>

## Accès
<option a : colonne `is_admin` + middleware `admin`.>
<options b/c/d : spatie/laravel-permission. Rôles dans `Enums/RoleEnum`, permissions dans `Enums/PermissionEnum`, format `ressource.action`. Vérifier avec `$user->can()`, `@can`, Policies, middleware `role:` / `permission:`. Après tout ajout : relancer `php artisan db:seed --class=RolesAndPermissionsSeeder`.>

## Règles
- Ne jamais écraser un fichier existant sans le dire.
- Pas de mot de passe ni de clé en dur : toujours `.env`.
- `.gitkeep` seulement dans les dossiers vides.
- Respecter Laravel Pint (`./vendor/bin/pint`) avant chaque commit.
````

`docs/ARCHITECTURE.md` : même contenu, rédigé pour un humain (titre « Architecture du projet », sans les consignes adressées à Claude).

---

## Options — détail de mise en œuvre

### Option 3 — Langue
- **français** : `APP_LOCALE=fr`, `APP_FALLBACK_LOCALE=fr`, `APP_FAKER_LOCALE=fr_FR`. Traductions des messages Laravel : `composer require laravel-lang/common --dev` puis `php artisan lang:add fr` ; si ces commandes échouent, `php artisan lang:publish` puis créer `lang/fr/` (auth, pagination, passwords, validation) en traduisant les fichiers de `lang/en/`.
- **anglais** : `APP_LOCALE=en`, `php artisan lang:publish`.
- **bilingue** : comme français + anglais, puis :
  - `lang/fr.json` et `lang/en.json` (textes de l'interface, clé = texte français)
  - `app/Http/Middleware/SetLocale.php` : lit `session('locale')`, vérifie qu'elle est dans `['fr', 'en']`, appelle `app()->setLocale()`. L'ajouter au groupe web dans `bootstrap/app.php` : `$middleware->web(append: [\App\Http\Middleware\SetLocale::class]);`
  - route `Route::get('/langue/{locale}', ...)->whereIn('locale', ['fr', 'en'])->name('locale.switch');` qui stocke en session puis `back()`
  - composant `<x-ui.language-switcher />` (FR | EN)
  - paquet `spatie/laravel-translatable` (contenu en base en FR + EN)

### Option 4 — Thème clair / sombre (oui)
- `resources/css/tokens.css` : variables `--bg`, `--bg-secondary`, `--surface`, `--border`, `--text`, `--text-muted`, `--accent`, `--accent-contrast` définies sur `:root` (clair) et `[data-theme="dark"]` (sombre). Importé par `web.css`.
- `resources/js/theme.js` : lit `localStorage.theme`, sinon `prefers-color-scheme` ; applique `document.documentElement.dataset.theme` ; expose une fonction de bascule ; protège chaque accès à `localStorage` par try/catch. Importé par `web.js` et `app.js`.
- Script anti-flash inline dans le `<head>` de `layouts/web.blade.php`, `layouts/app.blade.php` et `layouts/guest.blade.php` (avant le CSS) :
  ```html
  <script>try{var t=localStorage.getItem('theme')||(matchMedia('(prefers-color-scheme: dark)').matches?'dark':'light');document.documentElement.dataset.theme=t;}catch(e){}</script>
  ```
- `<x-ui.theme-toggle />` : bouton lune/soleil avec `aria-label`.
- Admin Tailwind : Tailwind 3 → `darkMode: ['selector', '[data-theme="dark"]']` dans `tailwind.config.js` ; Tailwind 4 → `@custom-variant dark (&:where([data-theme=dark], [data-theme=dark] *));` dans `app.css`.
- daisyUI utilise nativement `data-theme` ; Bootstrap → mettre aussi `data-bs-theme` sur `<html>` (le gérer dans `theme.js`).

### Option 5 — Style du site public (l'admin reste Tailwind)
- **tailwind** : `web.css` importe Tailwind (même version que l'admin) + `tokens.css`. Vérifier que la config Tailwind scanne `resources/views/**/*.blade.php`.
- **daisyui** : `npm i -D daisyui@<version compatible>` (v4 pour Tailwind 3 → `plugins: [require('daisyui')]` ; v5 pour Tailwind 4 → `@plugin "daisyui";` dans `web.css`). Composants en classes daisyUI (`btn`, `card`, `navbar`).
- **bootstrap** : `npm i bootstrap @popperjs/core` ; `web.css` → `@import 'bootstrap/dist/css/bootstrap.min.css';` + `tokens.css` ; `web.js` → `import 'bootstrap';`. Ne jamais l'importer dans `app.css`/`app.js`.
- **css** : CSS pur dans `web.css` basé sur `tokens.css` (idéal pour intégrer une maquette existante).

### Option 7 — Hébergement OVH mutualisé
- `.env` : `QUEUE_CONNECTION=sync` (pas de worker permanent).
- `docs/DEPLOIEMENT.md` : checklist — dossier racine du multisite OVH pointé sur `public/` ; PHP ≥ version requise dans `.ovhconfig` ; `composer install --no-dev --optimize-autoloader` ; `.env` de production (`APP_ENV=production`, `APP_DEBUG=false`) ; `php artisan migrate --force` ; `php artisan config:cache route:cache view:cache` ; `npm run build` en local puis envoi de `public/build` ; **ne jamais écraser `database/database.sqlite` de production** ; droits d'écriture sur `storage/`, `bootstrap/cache/` et `database/` ; si `php artisan storage:link` échoue (liens symboliques), servir les fichiers publics autrement (ex. disque dans `public/uploads`) ; tâche planifiée OVH (cron) appelant `php artisan schedule:run` si backups ou tâches planifiées.
- VPS / autre : checklist générique (Nginx/Apache vers `public/`, Supervisor pour les queues, cron).

### Option 8 — Gestion des accès
- **a) admin simple** : migration `add_is_admin_to_users_table` (`boolean('is_admin')->default(false)`), cast `'is_admin' => 'boolean'` dans `User`, `app/Http/Middleware/EnsureUserIsAdmin.php` (`abort(403)` si non admin), alias `'admin'` dans `bootstrap/app.php` (`$middleware->alias([...])`), seeder `AdminUserSeeder` (compte depuis `ADMIN_*` du `.env`, `is_admin = true`).
- **b / c / d** : `composer require spatie/laravel-permission` ; `php artisan vendor:publish --provider="Spatie\Permission\PermissionServiceProvider"` ; trait `HasRoles` sur `User` ; alias middleware :
  ```php
  'role' => \Spatie\Permission\Middleware\RoleMiddleware::class,
  'permission' => \Spatie\Permission\Middleware\PermissionMiddleware::class,
  'role_or_permission' => \Spatie\Permission\Middleware\RoleOrPermissionMiddleware::class,
  ```
  - `Enums/RoleEnum.php` (b, d) — cas issus de la liste de rôles.
  - `Enums/PermissionEnum.php` (c, d) — pour chaque ressource : `ressource.voir`, `ressource.creer`, `ressource.modifier`, `ressource.supprimer` ; plus `admin.acceder`.
  - `database/seeders/RolesAndPermissionsSeeder.php` : `app()[\Spatie\Permission\PermissionRegistrar::class]->forgetCachedPermissions()`, crée rôles/permissions depuis les Enums (`firstOrCreate`), attribue les permissions aux rôles (d), crée le compte admin depuis `.env` et lui donne le rôle `admin` (b, d) ou toutes les permissions (c). L'appeler depuis `DatabaseSeeder`.
  - **d** : super-admin dans `AppServiceProvider::boot()` → `Gate::before(fn ($user) => $user->hasRole(RoleEnum::ADMIN->value) ? true : null);`
  - Écrans admin Blade : `admin/users/` (liste, attribuer rôles/permissions) et `admin/roles/` (b, d) avec controllers `Admin/UserController`, `Admin/RoleController`, FormRequests et Services/Repositories correspondants (respecter l'architecture).

### Option 9 — Packs de paquets
- **vitrine** : `artesaos/seotools` (balises SEO + Open Graph ; publier sa config) · `spatie/laravel-sitemap` (+ commande `app/Console/Commands/GenerateSitemap.php` planifiée) · `spatie/laravel-sluggable` · `intervention/image` · `spatie/laravel-settings` (publier ses migrations ; créer `app/Settings/GeneralSettings.php` avec téléphone, email, WhatsApp, adresse, réseaux sociaux + sa migration dans `database/settings/` ; écran `admin/parametres/`) · `blade-ui-kit/blade-icons` + `mallardduck/blade-lucide-icons`.
- **gestion** : `barryvdh/laravel-dompdf` (dossier `views/pdf/`) · `maatwebsite/excel` · `spatie/laravel-activitylog` (publier migrations) · `spatie/laravel-backup` (publier config ; planifier dans `routes/console.php`).
- Automatiques : bilingue → `spatie/laravel-translatable` ; accès b/c/d → `spatie/laravel-permission`.
- Pour chacun : installer, publier config/migrations si nécessaire, ajouter une ligne dans `CLAUDE.md` (section Configuration) expliquant son usage.

### Option 11 — Extras
- **dev** : `composer require --dev barryvdh/laravel-debugbar larastan/larastan barryvdh/laravel-ide-helper` (si un nom de paquet a changé, vérifier sur Packagist) ; créer `phpstan.neon` (include larastan, `level: 5`, `paths: [app]`) ; `php artisan ide-helper:generate` ; ajouter `_ide_helper.php` et `.phpstorm.meta.php` au `.gitignore`. Rappeler que Debugbar ne s'affiche que si `APP_DEBUG=true`.
- **vscode** : `.vscode/extensions.json` recommandant `bmewburn.vscode-intelephense-client`, `onecentlab.laravel-blade`, `amiralizadeh9480.laravel-extra-intellisense`, `bradlc.vscode-tailwindcss` (si Tailwind/daisyUI), `editorconfig.editorconfig`.

---

## Étape 6 — Finalisation

1. `php artisan migrate` (puis `php artisan db:seed` si un seeder a été créé, après avoir vérifié que `ADMIN_*` est rempli dans `.env`).
2. `npm install` puis `npm run build` → doit réussir.
3. `php artisan test` → les tests Breeze doivent passer. Si un test échoue à cause du déplacement du dashboard, l'adapter.
4. `./vendor/bin/pint` (formatage).
5. Ajouter `.gitkeep` dans chaque dossier **vide** créé par le skill.
6. Git (option 10 = oui) : `git init` si nécessaire ; vérifier que `.env`, `/vendor`, `/node_modules`, `/public/build` et `database/*.sqlite` sont ignorés ; `git add -A` ; `git commit -m "chore: architecture initiale (/architecture-laravel)"`.

## Étape 7 — Résumé final

Affiche :
- l'arbre des dossiers créés (sans `vendor` ni `node_modules`) ;
- les paquets installés (et ceux refusés pour incompatibilité, avec l'alternative proposée) ;
- les fichiers existants laissés intacts ;
- les prochaines étapes concrètes (ex. « remplir ADMIN_PASSWORD dans .env », « lancer `php artisan serve` et ouvrir /admin », « créer votre première ressource avec la checklist du CLAUDE.md »).
