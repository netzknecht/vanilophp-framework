# Vanilo Category Module

[![Travis](https://img.shields.io/travis/vanilophp/category.svg?style=flat-square)](https://travis-ci.org/vanilophp/category)
[![Packagist version](https://img.shields.io/packagist/vpre/vanilo/category.svg?style=flat-square)](https://packagist.org/packages/vanilo/category)
[![Packagist downloads](https://img.shields.io/packagist/dt/vanilo/category.svg?style=flat-square)](https://packagist.org/packages/vanilo/category)
[![StyleCI](https://styleci.io/repos/145992208/shield?branch=master)](https://styleci.io/repos/145992208)
[![MIT Software License](https://img.shields.io/badge/license-MIT-blue.svg?style=flat-square)](LICENSE.md)

Category module for the [Vanilo E-commerce framework](https://vanilo.io)

[Documentation](https://vanilo.io/docs/master/categorization)

## Overview

Depending on your needs you may want to have a single category tree, or
more than one. As an example some shops prefer to have "brands" as a
hierarchical tree besides the usual "category" classification. Some
other shops use brand as a product attribute. Both solutions can be good
depending on the unique shop's needs.

Another example is a wine shop that classifies wines based on region and
on type.

Taken from other ecommerce systems, separate category trees are called
**Taxonomies** and their child branches are called **Taxons**.

**Example 1:**

```
Category                <- Taxonomy
│
├─> Men                 <- Taxon
│   └> T-shirts         <- Taxon
│   └> Jeans            <- Taxon
└─> Women               <- Taxon
    └> Skirts           <- Taxon
    └> Accessories      <- Taxon
```

**Example 2:**

```
Region                  <- Taxonomy
│
├─> France              <- Taxon
│   └> Bordeaux         <- Taxon
│   └> Côtes du Rhone   <- Taxon
└─> Italy               <- Taxon
    └> Veneto           <- Taxon
    └> Tuscany          <- Taxon
    └> Piedmont         <- Taxon
```

**Example 3:**

```
Type                    <- Taxonomy
│
├─> Red                 <- Taxon
│   └> Cabernet Franc   <- Taxon
│   └> Merlot           <- Taxon
│   └> Porto            <- Taxon
├─> White               <- Taxon
│   └> Muscat Ottonel   <- Taxon
│   └> Tokaji           <- Taxon
│   └> Furmint          <- Taxon
└─> Rosé                <- Taxon
    └> Cabernet Franc   <- Taxon
    └> Cuvée            <- Taxon
```

## Creating Taxonomies

Taxonomies basically have two properties: name and slug.
The slug must be unique and gets autogenerated (URL-ified) from the name
if it doesn't explicitly get specified.

```php
use Vanilo\Category\Models\Taxonomy;

$category = Taxonomy::create(['name' => 'Wine Regions']);

echo $category->name;
// Wine Regions
echo $category->slug;
// wine-regions
```

> The Taxonomy and the Taxon models use the
> [Eloquent Sluggable](https://github.com/cviebrock/eloquent-sluggable)
> package.

If you explicitly set the slug, no autogeneration will take place:

```php
use Vanilo\Category\Models\Taxonomy;

$category = Taxonomy::create(['name' => 'Wine Regions', 'slug' => 'regions']);

echo $category->slug;
// regions
```

In case a slug already exists, the slug will be automatically extended
to prevent duplicates:

```php
use Vanilo\Category\Models\Taxonomy;

$category1 = Taxonomy::create(['name' => 'Category']);
$category2 = Taxonomy::create(['name' => 'Category']);

echo $category1->slug;
// category

echo $category2->slug;
// category-1
```

## Finding Taxonomies

There's a dedicated finder method to retrieve a single taxonomy by name:

```php
Taxonomy::create(['name' => 'Brands']);

$brands = Taxonomy::findOneByName('Brands');
```

## Creating Taxons

Taxons are the actual category entries like "Smartphones" or "Riesling",
etc. Every Taxon must belong to a Taxonomy and must have a name.

```php
use Vanilo\Category\Models\Taxonomy;
use Vanilo\Category\Models\Taxon;

$category = Taxonomy::create(['name' => 'Category']);
$smartphones = Taxon::create([
    'taxonomy_id' => $category->id,
    'name' => 'Smartphones'
]);

// You can also use the setTaxonomy method:
$smartphones->setTaxonomy($category);
$smartphones->save();
```

To retrieve the taxonomy the taxon belongs to, use the `taxonomy` property:

```php
$category = Taxonomy::create(['name' => 'Category']);

$taxon = new Taxon();
$taxon->setTaxonomy($category);

echo get_class($taxon->taxonomy);
// Vanilo\Category\Models\Taxonomy

echo $taxon->taxonomy->name;
// Category
```

### Taxon Slug (URL)

Taxons also have a slug field to be used for their URLs, and work very
similar to Taxonomies.

If no value is given for the `slug` field, it gets autogenerated from
the value of the name field:

```php
$category = Taxonomy::create(['name' => 'Category']);

$monitors = new Taxon();
$monitors->name = 'Monitors';
$monitors->setTaxonomy($category);
$monitors->save();

echo $monitors->slug;
// monitors
```

If you explicitly set the slug, no autogeneration will take place:

```php
$taxon = Taxon::create([
    'taxonomy_id' => Taxonomy::create(['name' => 'Wine Regions']),
    'name' => 'Carcavelos DOC',
    'slug' => 'carcavelos'
]);

echo $taxon->slug;
// carcavelos
```

Taxon slugs must be unique within the same taxonomy and level.

**Example of same slug allowed:**
```
wine-type               <- Taxonomy Slug
│
├─> red                 <- Taxon Slug
│   └> cabernet-franc   <- Taxon Slug, duplicate ✔
└─> rose                <- Taxon Slug
    └> cabernet-franc   <- Taxon Slug, duplicate ✔
```

**Example of same slug forbidden:**
```
wine-type               <- Taxonomy Slug
│
├─> red                 <- Taxon Slug
│   └> cabernet         <- Taxon Slug, duplicate ❌
│   └> cabernet         <- Taxon Slug, duplicate ❌
└─> rose                <- Taxon Slug
    └> cabernet         <- Taxon Slug, duplicate ✔
```

### Taxon Parents

Taxons can optionally have one parent taxon they belong under.
Taxons that don't have a parent taxon are considered root level entries.

```php
use Vanilo\Category\Models\Taxonomy;
use Vanilo\Category\Models\Taxon;

$category = Taxonomy::create(['name' => 'Category']);

$audio = Taxon::create([
    'taxonomy_id' => $category->id,
    'name' => 'Audio'
]);

$speakers = Taxon::create([
    'taxonomy_id' => $category->id,
    'parent_id' => $audio->id,
    'name' => 'Speakers'
]);

echo get_class($speakers->parent);
// Vanilo\Category\Models\Taxon
echo $speakers->parent->name;
// Audio
```

Other than setting the `parent_id` field directly, it is also possible to call the setter method:

```php
$childTaxon->setParent($parentTaxon);
$childTaxon->save();
```

To dissociate the parent use:

```php
$childTaxon->removeParent();
$childTaxon->save();

var_dump($childTaxon->parent_id);
// NULL
var_dump($childTaxon->parent);
// NULL
```

### Taxon Children

Since taxons are a tree type of hierarchy, they can have multiple children.

The `children` property returns a Collection of child taxons.

```php
$category = Taxonomy::create(['name' => 'Category']);

$topLevelTaxon = Taxon::create([
    'taxonomy_id' => $category->id,
    'name' => 'Rigging'    
]);

$childTaxon1 = Taxon::create([
    'taxonomy_id' => $category->id,
    'parent_id' => $topLevelTaxon->id,
    'name' => 'Halyards'
]);

$childTaxon1 = Taxon::create([
    'taxonomy_id' => $category->id,
    'parent_id' => $topLevelTaxon->id,
    'name' => 'Sheets'
]);

foreach ($topLevelTaxon->children as $child) {
    echo "{$child->name}\n";
}
// Halyards
// Sheets
```

### Taxon Level

Taxons can tell their level in the hierarchy.

Top level entries (without parent) are on level 0, their children are
level 1, and so on.

```php
$category = Taxonomy::create(['name' => 'Category']);

$audio = Taxon::create([
    'taxonomy_id' => $category->id,
    'name' => 'Audio'
]);

$speakers = Taxon::create([
    'taxonomy_id' => $category->id,
    'parent_id' => $audio->id,
    'name' => 'Speakers'
]);

echo $audio->level;
// 0

var_dump($audio->isRootLevel());
// true

echo $speakers->level;
// 1
```

### Neighbours

Neighbours are the taxons which are under a common parent (within the same taxonomy).

It is defined as a [HasMany Eloquent relationship](https://laravel.com/docs/5.7/eloquent-relationships#one-to-many)
thus available as a property (`$taxon->neighbours`) which gives a collection.

> Due to the internals of relationships, the relationship doesn't work for root level taxons (`parent_id == NULL`)

```php
$books = Taxon::create(['name' => 'Books']);

Taxon::create(['name' => 'Sci-fi', 'parent_id' => $books->id]);
Taxon::create(['name' => 'Thriller', 'parent_id' => $books->id]);
$fantasy = Taxon::create(['name' => 'Fantasy', 'parent_id' => $books->id]);

$fantasy->neighbours;
// Sci-fi
// Thriller
// Fantasy

// Yes, it returns the caller itself as well (read below how to filter it)
```

It is also possible to invoke `$taxon->neighbours()` as a method and further use it as query builder:

```php
$taxon->neighbours()->get();

// To exclude the caller from the result use the `except` scope:
$taxon->neighbours()->except($taxon)->get();

// To get them in a reverse order
$taxon->neighbours()->sortReverse()->get();
```

#### Get First And Last Neighbours

> Unlike the `neighbours` relationship, this works properly on root level taxons as well

There are two dedicated methods to retrieve the first or the last neighbour:

- `$taxon->lastNeighbour()` and
- `$taxon->firstNeighbour()`

The order of the taxons is based on the `priority` field.

```php
$gadgets = Taxon::create(['Gadgets']);

$laptops = Taxon::create(['name' => 'Laptops', 'priority' => 1, 'parent_id' => $gadgets->id]);
$watches = Taxon::create(['name' => 'Watches', 'priority' => 2, 'parent_id' => $gadgets->id]);
$phones  = Taxon::create(['name' => 'Phones', 'priority' => 3, 'parent_id' => $gadgets->id]);
$tablets = Taxon::create(['name' => 'Tablets', 'priority' => 4, 'parent_id' => $gadgets->id]);

echo $phones->firstNeighbour()->name;
// Laptops
echo $phones->lastNeighbour()->name;
// Tablets

// It may return itself if that happens to be the case:
echo $laptops->firstNeighbour()->name;
// Laptops
echo $tablets->lastNeighbour()->name;
// Tablets

// To exclude itself from the result, set the `$excludeSelf` parameter of the method to true:
echo $laptops->firstNeighbour(true)->name;
// Watches
echo $tablets->lastNeighbour(true)->name;
// Phones
```

### Retrieving Taxons (Scopes)

The default Taxon model that ships with this package defines a several [Query Scopes](https://laravel.com/docs/5.7/eloquent#local-scopes).

Due to the nature of Eloquent query scopes, these are chainable so it is possible to combine them arbitrarily.

#### Retrieve By Taxonomy

To retrieve all taxons belonging to a taxonomy, use the `byTaxonomy` scope:

```php
$category = Taxonomy::findOneByName('Category');

// Returns a collection of taxons
$taxons = Taxon::byTaxonomy($category)->get();

// The method also works by passing the taxonomy id:
$id = $category->id;
$taxons = Taxon::byTaxonomy($id)->get();
```

#### Retrieve Root Level Taxons

An alternative to `$taxonomy->rootLevelTaxons()` is to retrieve all the root level taxons:

```php
// It returns all the taxons without parent, from all taxonomies:
$allRootLevelTaxons = Taxon::roots()->get();

// It is possible of course to combine with byTaxonomy scope:
$taxonomy = Taxonomy::findOneByName('Brands');
$rootTaxonsForBrands = Taxon::roots()->byTaxonomy($taxonomy)->get();
```

#### Sorting Taxons

Taxons have a field called `priority` which is designed to make taxons sortable.

The `sort()` and `sortReverse()` query scopes sort results by priority:

```php
$spirits = Taxonomy::create(['name' => 'Spirits']);
Taxon::create(['name' => 'Gin', 'priority' => 3, 'taxonomy_id' => $spirits->id]);
Taxon::create(['name' => 'Whisky', 'priority' => 1, 'taxonomy_id' => $spirits->id]);
Taxon::create(['name' => 'Armagnac', 'priority' => 2, 'taxonomy_id' => $spirits->id]);

foreach(Taxon::sort()->get() as $taxon) {
    echo $taxon->name . "\n";
}
// Output:
// Whisky
// Armagnac
// Gin

// To get taxons in reverse order: 
foreach(Taxon::sortReverse()->get() as $taxon) {
    echo $taxon->name . "\n";
}
// Output:
// Gin
// Armagnac
// Whisky
```

#### Exclude One Taxon From The List

There are cases when you want to exclude a taxon from the list of taxons.
For that purpose you can utilize the `except(Taxon $taxon)` scope:

```php
$me = Taxon::create(['name' => 'Me']);

Taxon::create(['name' => 'You']);
Taxon::create(['name' => 'She']);
Taxon::create(['name' => 'We']);

Taxon::except($me)->get();
// You
// She
// We
```

## Known Issues

### Duplicate Taxon Slugs On Root Level

Uniqueness of taxon slugs within a taxonomy level is currently
guaranteed by unique DB keys. Most
[contemporary DB engines allow NULLs in composite unique keys](https://sqlite.org/faq.html#q26).

Therefore root level taxons can have duplicate slugs.

### Neighbours Relationship On Root Level

The `neighbours()` relationship does not work on root level taxons.
It returns an empty result set.
