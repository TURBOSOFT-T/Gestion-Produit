# Gestion-Produit
Dans ce tutoriel, il s'agit d'une application en Laravel 8 qui permet de:
 - La gestion des rôles
 - La gestion des utilisateurs
 - La gestion des produits

Notre sujet principal est le tutoriel sur les rôles et les autorisations de Laravel 8. Dans cet article, nous allons implémenter un didacticiel sur les rôles et les autorisations des utilisateurs de laravel 8 spatie. j'ai expliqué simplement étape par étape laravel 8 spatie/laravel-permission. expliquer étape par étape le didacticiel laravel 8 acl.

Nous utilisons le package spatie github pour les rôles et les autorisations dans l'application laravel 8. suivez simplement l'étape ci-dessous pour créer acl dans laravel 8.

Le package de composition d'autorisation de rôle Spatie fournit un moyen de créer une liste de contrôle d'accès dans laravel 8. ils indiquent comment attribuer un rôle à un utilisateur, comment attribuer une autorisation à un utilisateur et comment attribuer une autorisation à un rôle. j'écrirai étape par étape en créant des rôles et des autorisations dans l'application laravel 8.

Rôles et autorisations via vous pouvez créer plusieurs types d'utilisateurs avec des rôles et des autorisations différents, je veux dire que certains utilisateurs n'ont que le module de liste des éléments, certains utilisateurs peuvent également modifier les modules d'éléments, les supprimer, etc.


    Autorisations Laravel - L'utilisateur n'a pas les bons rôles

 
J'essaie de trouver une solution dans un cas d'utilisation où l'utilisateur administrateur n'a pas le rôle attribué. Dans la vue du tableau de bord, l'URL n'est pas rendue pour les utilisateurs, tandis que si j'accède directement au tableau de bord/aux utilisateurs, j'obtiens :

Spatie \ Permission \ Exceptions \ UnauthorizedException user does not have the right roles

app/Http/Kernel.php

    protected $routeMiddleware = [
     ....
    'role' => \Spatie\Permission\Middlewares\RoleMiddleware::class,
    'permission' => \Spatie\Permission\Middlewares\PermissionMiddleware::class,
];
routes/web.php

Route::group(
   ['middleware' => ['role:admin']],
    function () {
      Route::get('/dashboard/users', 'UsersController@index');
      Route::get('/dashboard/users/{id}', 'UsersController@edit');
      Route::patch('/dashboard/users/{id}', 'UsersController@update');
   }
);
view/dashboard.blade.php

<div class="panel-body">
  @hasrole('admin')
     <li><a href="/dashboard/users">Manage Users</a></li>
  @endhasrole
</div>
J'ai généré avec succès les rôles et autorisations par défaut avec

Commands/GenerateRoles.php

    public function handle()
    {
    $this->info('Generating default roles and permissions...');
    $admin = User::create(
      [
        'name'     => 'administrator',
        'email'    => 'adm@mail.com',
        'password' => bcrypt('12345'),
      ]
    );

    // Create roles.
    $adminRole   = Role::create(['name' => 'admin']);
    $supportRole = Role::create(['name' => 'support']);

    $admin->assignRole('admin');

    // Create permissions.
    $userManagement = Permission::create(['name' => 'users management']);
    $deleteImages  = Permission::create(['name' => 'delete images']);
    $datasetStatus   = Permission::create(
      ['name' => 'change dataset building status']
    );

    $adminRole->givePermissionTo($userManagement);
    $deleteImages->syncRoles([$adminRole, $supportRole]);
    $datasetStatus->syncRoles([$adminRole, $supportRole]);
   }