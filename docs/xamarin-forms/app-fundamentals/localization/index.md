---
title : « Xamarin.Forms Localization » Description : «the Built-in .net localization Framework peut être utilisé pour créer des applications multilingues multiplateformes avec Xamarin.Forms . Le texte et les images peuvent être localisés, et les applications peuvent prendre en charge un sens de déroulement de droite à gauche.»
ms. Prod : xamarin ms. AssetID : 97BF843B-BDAA-4CEA-8189-6DB54B291D7F ms. Technology : xamarin-Forms Author : davidbritch ms. Author : dabritch ms. Date : 11/07/2018 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-localization"></a>Xamarin.FormsLocaliz

_L’infrastructure de localisation .NET intégrée peut être utilisée pour créer des applications multilingues multiplateformes avec Xamarin.Forms ._

## <a name="xamarinforms-string-and-image-localizationtextmd"></a>[Xamarin.FormsLocalisation de chaînes et d’images](text.md)

Le mécanisme intégré de localisation des applications .NET utilise des [fichiers RESX](https://docs.microsoft.com/dotnet/framework/resources/creating-resource-files-for-desktop-apps#resources-in-resx-files) ainsi que les classes des espaces de noms `System.Resources` et `System.Globalization`. Les fichiers RESX contenant des chaînes traduites sont incorporés dans l' Xamarin.Forms assembly, avec une classe générée par le compilateur qui fournit un accès fortement typé aux traductions. Vous pouvez ensuite récupérer le texte traduit dans le code.

## <a name="right-to-left-localization"></a>[Localisation de droite à gauche](right-to-left.md)

La direction de flux est la direction dans laquelle les éléments d’IU sur la page sont analysés par l’œil. La localisation de droite à gauche ajoute la prise en charge du sens du déroulement de droite à gauche aux Xamarin.Forms applications.
