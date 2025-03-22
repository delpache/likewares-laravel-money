# Formatage et conversion de devises pour Laravel

Ce package a pour but de fournir des outils de formatage et de conversion des valeurs monétaires d'une manière simple et puissante pour les projets Laravel.

### Pourquoi ne pas utiliser le package `moneyphp` ?

Parce qu'il utilise l'extension `intl` pour le formatage des nombres. L'extension `intl` n'est pas présente par défaut dans les installations de PHP et peut donner [des résultats différents](http://moneyphp.org/en/latest/features/formatting.html#intl-formatter) sur différents serveurs.

## Démarrer

### 1. Installation

Exécutez la commande suivante :

```bash
composer require likewares/laravel-money
```

### 2. Publication

Publication du fichier de configuration.

```bash
php artisan vendor:publish --tag=money
```

### 3. Configuration

Vous pouvez modifier les informations relatives aux devises de votre application dans le fichier `config/money.php`.

## Usage

```php
use Likewares\Money\Currency;
use Likewares\Money\Money;


echo Money::USD(500) ; // '$5.00' non converti
echo new Money(500, new Currency('USD')) ; // '$5.00' non converti
echo Money::USD(500, true) ; // '500.00' converti
echo new Money(500, new Currency('USD'), true) ; // '500.00' converti
```

### Avancé

```php
$m1 = Money::USD(500);
$m2 = Money::EUR(500);

$m1->getCurrency();
$m1->isSameCurrency($m2);
$m1->compare($m2);
$m1->equals($m2);
$m1->greaterThan($m2);
$m1->greaterThanOrEqual($m2);
$m1->lessThan($m2);
$m1->lessThanOrEqual($m2);
$m1->convert(Currency::GBP(), 3.5);
$m1->add($m2);
$m1->subtract($m2);
$m1->multiply(2);
$m1->divide(2);
$m1->allocate([1, 1, 1]);
$m1->isZero();
$m1->isPositive();
$m1->isNegative();
$m1->format();
```

### Helpers

```php
money(500)
money(500, 'USD')
currency('USD')
```

### Blade Directives

```php
@money(500)
@money(500, 'USD')
@currency('USD')
```

### Composant Blade

Comme pour la directive, il existe également un composant `blade` qui vous permet de créer de l'argent et de la monnaie dans vos vues :

```html
<x-money amount="500" />
ou
<x-money amount="500" currency="USD" />
ou
<x-money amount="500" currency="USD" convert />

<x-currency currency="USD" />
```

### Macros

Ce package implémente le trait Laravel `Macroable`, permettant des macros et des mixins sur `Money` et `Currency`.

Exemple de cas d'utilisation :

```php
use Likewares\Money\Currency;
use Likewares\Money\Money;

Money::macro(
    'absolute',
    fn () => $this->isPositive() ? $this : $this->multiply(-1)
);

$money = Money::USD(1000)->multiply(-1);

$absolute = $money->absolute();
```

Les macros peuvent également être appelées de manière statique :

```php
use Likewares\Money\Currency;
use Likewares\Money\Money;

Money::macro('zero', fn (?string $currency = null) => new Money(0, new Currency($currency ?? 'GBP')));

$money = Money::zero();
```

### Mixins

Outre les macros, les mixins sont également pris en charge. Cela permet de fusionner les méthodes d'une autre classe dans la classe Money ou Currency.

Définir la classe mixin :

```php
use Likewares\Money\Money;

class CustomMoney
{
    public function absolute(): Money
    {
        return $this->isPositive() ? $this : $this->multiply(-1);
    }

    public static function zero(?string $currency = null): Money
    {
        return new Money(0, new Currency($currency ?? 'GBP'));
    }
}
```

Enregistrez le mixin en lui passant une instance de la classe :

```php
Money::mixin(new CustomMoney);
```

Les méthodes de la classe personnalisée seront disponibles :

```php
$money = Money::USD(1000)->multiply(-1);
$absolute = $money->absolute();

// Les méthodes statiques via les mixins sont également prises en charge :
$money = Money::zero();
```

## License

La licence MIT (MIT). Veuillez consulter [LICENSE](LICENSE.md) pour plus d'informations.
