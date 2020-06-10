---
title : "Description des préfixes d’espace de noms XAML dans Xamarin.Forms " Description : "la classe XmlnsPrefixAttribute peut être utilisée par les auteurs de contrôle pour spécifier un préfixe recommandé à associer à un espace de noms XAML, pour l’utilisation de XAML."
ms. Prod : xamarin ms. AssetID : 7B315BEC-7A35-48F4-A9C7-EF40255E95FF ms. Technology : xamarin-Forms Author : davidbritch ms. Author : dabritch ms. Date : 02/28/2019 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xaml-namespace-recommended-prefixes-in-xamarinforms"></a>Préfixes d’espace de noms XAML recommandés dansXamarin.Forms

La `XmlnsPrefixAttribute` classe peut être utilisée par les auteurs de contrôle pour spécifier un préfixe recommandé à associer à un espace de noms XAML, pour l’utilisation de XAML. Le préfixe est utile lors de la prise en charge de la sérialisation de l’arborescence d’objets en XAML, ou lors de l’interaction avec un environnement de conception qui a des fonctionnalités d’édition XAML. Par exemple :

- Les éditeurs de texte XAML peuvent utiliser `XmlnsPrefixAttribute` comme indicateur pour un mappage d’espace de noms XAML initial `xmlns` .
- Les environnements de conception XAML peuvent utiliser le `XmlnsPrefixAttribute` pour ajouter des mappages au code XAML lorsque vous faites glisser des objets en dehors d’une boîte à outils et sur une aire de conception visuelle.

Les préfixes d’espace de noms recommandés doivent être définis au niveau de l’assembly avec le `XmlnsPrefixAttribute` constructeur, qui accepte deux arguments : une chaîne qui spécifie l’identificateur d’un espace de noms XAML et une chaîne qui spécifie un préfixe recommandé :

```csharp
[assembly: XmlnsPrefix("http://xamarin.com/schemas/2014/forms", "xf")]
```

Les préfixes doivent utiliser des chaînes courtes, car le préfixe est généralement appliqué à tous les éléments sérialisés qui proviennent de l’espace de noms XAML. Par conséquent, la longueur de la chaîne de préfixe peut avoir un effet notable sur la taille de la sortie XAML sérialisée.

> [!NOTE]
> Plusieurs `XmlnsPrefixAttribute` peuvent être appliqués à un assembly. Par exemple, si vous avez un assembly qui définit des types pour plusieurs espaces de noms XAML, vous pouvez définir des valeurs de préfixe différentes pour chaque espace de noms XAML.

## <a name="related-links"></a>Liens connexes

- [Schémas d’espace de noms personnalisés XAML](custom-namespace-schemas.md)
- [Espaces de noms XAML dansXamarin.Forms](namespaces.md)
