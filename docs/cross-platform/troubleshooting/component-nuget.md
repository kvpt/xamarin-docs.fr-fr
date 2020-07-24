---
title: Mise à jour des références de composants à NuGet
description: Ce document explique comment remplacer les références de votre composant par des packages NuGet dans vos applications, car le magasin de composants Xamarin a été supprimé.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 9E6C986F-3FBA-4599-8367-FB0C565C0ADE
author: davidortinau
ms.author: daortin
ms.date: 04/18/2018
ms.openlocfilehash: b9b771efe338fbcc250aa6e7a83b73f35269d3bf
ms.sourcegitcommit: 952db1983c0bc373844c5fbe9d185e04a87d8fb4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "86996810"
---
# <a name="updating-component-references-to-nuget"></a>Mise à jour des références de composants à NuGet

> [!IMPORTANT]
> Le magasin de composants a été supprimé à partir du 15 mai 2018 (cette clôture a été [annoncée](https://blog.xamarin.com/hello-nuget-new-home-xamarin-components/) à l’origine en novembre 2017).
>
> Les composants Xamarin ne sont plus pris en charge dans Visual Studio et doivent être remplacés par des packages NuGet. Suivez les instructions ci-dessous pour supprimer manuellement les références de composants de vos projets.

Reportez-vous à ces instructions pour l’ajout de packages NuGet sur [Windows](https://docs.microsoft.com/nuget/quickstart/use-a-package) ou [Mac](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough).

Une liste de plug- [ins et de bibliothèques](https://github.com/xamarin/XamarinComponents/blob/master/README.md) Xamarin populaires est disponible pour vous aider à trouver des alternatives aux composants qui ne sont pas disponibles sous forme de packages NuGet.

## <a name="manually-removing-component-references"></a>Suppression manuelle des références de composant

La version 15,6 de Visual Studio et de la version 7,4 de Visual Studio pour Mac ne prend plus en charge les composants de votre projet.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Si vous chargez un projet dans Visual Studio, la boîte de dialogue suivante s’affiche, expliquant que vous devez supprimer manuellement les composants de votre projet :

![Boîte de dialogue d’alerte indiquant qu’un composant a été trouvé dans votre projet et doit être supprimé](component-nuget-images/component-alert-vs.png)

Pour supprimer un composant de votre projet :

1. Ouvrez le fichier  **.csproj**. Pour ce faire, cliquez avec le bouton droit sur le nom du projet, puis sélectionnez **décharger le projet**.

2. Cliquez à nouveau avec le bouton droit sur le projet déchargé, puis sélectionnez **modifier {Your-Project-Name}. csproj**.

3. Recherchez dans le fichier toutes les références à `XamarinComponentReference` . Elle doit ressembler à l’exemple suivant :

    ```xml
    <ItemGroup>
      <XamarinComponentReference Include="advancedcolorpicker">
        <Version>2.0.1</Version>
        <Visible>False</Visible>
      </XamarinComponentReference>
      <XamarinComponentReference Include="gunmetaltheme">
        <Version>1.4.1</Version>
        <Visible>False</Visible>
      </XamarinComponentReference>
      <XamarinComponentReference Include="signature-pad">
        <Version>2.2.0</Version>
        <Visible>False</Visible>
      </XamarinComponentReference>
    </ItemGroup>
    ```

4. Supprimez les références à `XamarinComponentReference` et enregistrez le fichier. Dans l’exemple ci-dessus, il est possible de supprimer la totalité de l’ensemble `ItemGroup` .

5. Une fois le fichier enregistré, cliquez avec le bouton droit sur le nom du projet, puis sélectionnez **recharger le projet**.

6. Répétez les étapes ci-dessus pour chaque projet de votre solution.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

Si vous chargez un projet dans Visual Studio pour Mac, la boîte de dialogue suivante s’affiche, expliquant que vous devez supprimer manuellement les composants de votre projet :

![Boîte de dialogue d’alerte indiquant qu’un composant a été trouvé dans votre projet et doit être supprimé](component-nuget-images/component-alert.png)

Pour supprimer un composant de votre projet :

1. Ouvrez le fichier .csproj. Pour ce faire, cliquez avec le bouton droit sur le nom du projet, puis sélectionnez **outils > modifier le fichier**.

2. Recherchez dans le fichier toutes les références à `XamarinComponentReference` . Elle doit ressembler à l’exemple suivant :

    ```xml
    <ItemGroup>
      <XamarinComponentReference Include="advancedcolorpicker">
        <Version>2.0.1</Version>
        <Visible>False</Visible>
      </XamarinComponentReference>
      <XamarinComponentReference Include="gunmetaltheme">
        <Version>1.4.1</Version>
        <Visible>False</Visible>
      </XamarinComponentReference>
      <XamarinComponentReference Include="signature-pad">
        <Version>2.2.0</Version>
        <Visible>False</Visible>
      </XamarinComponentReference>
    </ItemGroup>
    ```

3. Supprimez les références à `XamarinComponentReference` et enregistrez le fichier. Dans l’exemple ci-dessus, il est possible de supprimer entièrement la`ItemGroup`

4. Répétez les étapes ci-dessus pour chaque projet de votre solution.

-----

> [!WARNING]
> Les instructions suivantes fonctionnent uniquement avec les versions antérieures de Visual Studio.
> Le nœud **composants** n’est plus disponible dans les versions actuelles de Visual Studio 2017 ou Visual Studio pour Mac.

Les sections suivantes expliquent comment mettre à jour les solutions Xamarin existantes pour modifier les références de composants aux packages NuGet.

- [Composants qui contiennent des packages NuGet](#contain)
- [Composants avec remplacements NuGet](#replace)

La plupart des composants appartiennent à l’une des catégories ci-dessus.
Si vous utilisez un composant qui ne semble pas avoir un package NuGet équivalent, lisez la section [composants sans chemin de migration NuGet](#require-update) ci-dessous.

<a name="contain"></a>

## <a name="components-that-contain-nuget-packages"></a>Composants qui contiennent des packages NuGet

De nombreux composants contiennent déjà des packages NuGet, et le chemin de migration consiste simplement à supprimer la référence de composant.

Vous pouvez déterminer si le composant contient déjà un package NuGet en double-cliquant sur le composant dans la solution :

![Nœud composants développé](component-nuget-images/solution-sml.png)

L’onglet **packages** répertorie tous les packages NuGet inclus dans le composant :

![L’onglet packages contient NuGet](component-nuget-images/packages-tab-sml.png)

Notez que l’onglet **assemblys** est vide :

![L’onglet assemblys est vide](component-nuget-images/assemblies-tab-empty-sml.png)

### <a name="updating-the-solution"></a>Mise à jour de la solution

Pour mettre à jour votre solution, supprimez l’entrée de **composant** de la solution :

![Supprimer le composant](component-nuget-images/delete-component-sml.png)

Le package NuGet restera listé dans le nœud **packages** et votre application se compilera et s’exécutera normalement. À l’avenir, les mises à jour de ce package seront effectuées à l’aide de la fonctionnalité de mise à jour de **NuGet** :

![Mettre à jour le package NuGet](component-nuget-images/nuget-update-sml.png)

<a name="replace"></a>

## <a name="components-with-nuget-replacements"></a>Composants avec remplacements NuGet

Si l’onglet **assemblys** de la page d’informations du composant contient des entrées comme indiqué ci-dessous, vous devrez rechercher manuellement le package NuGet équivalent.

![Contient des assemblys](component-nuget-images/assemblies-tab-sml.png)

Notez que l’onglet **packages** est probablement vide :

![Onglet packages](component-nuget-images/packages-tab-empty-sml.png)

_Il peut contenir des dépendances NuGet, mais celles-ci peuvent être ignorées._

Pour confirmer l’existence d’un package NuGet de remplacement, recherchez [NuGet.org](https://www.nuget.org/packages), en utilisant le nom du composant ou bien, par auteur.

Par exemple, vous pouvez trouver le package **SQLite-net-PCL** populaire en recherchant :

- [`sqlite-net-pcl`](https://www.nuget.org/packages?q=sqlite-net-pcl): nom du produit.
- [`praeclarum`](https://www.nuget.org/packages?q=praeclarum): profil de l’auteur.

### <a name="updating-the-solution"></a>Mise à jour de la solution

Une fois que vous avez confirmé que le composant est disponible dans NuGet, procédez comme suit :

#### <a name="delete-the-component"></a>Supprimer le composant

Cliquez avec le bouton droit sur le composant dans la solution, puis choisissez **supprimer**:

![Supprimer le composant](component-nuget-images/remove-component-sml.png)

Cette opération supprime le composant et toutes les références. Cela entraînera l’arrêt de votre Build, jusqu’à ce que vous ajoutiez le package NuGet équivalent pour le remplacer.

#### <a name="add-the-nuget-package"></a>Ajouter le package NuGet

1. Cliquez avec le bouton droit sur le nœud **packages** et choisissez **Ajouter des packages...**.
2. Recherchez le remplacement NuGet par son nom ou son auteur :

    ![Recherche NuGet](component-nuget-images/nuget-search-sml.png)

3. Appuyez sur **Ajouter un package**.

Le package NuGet sera ajouté à votre projet, ainsi que toutes les dépendances.
Cela devrait résoudre la Build. Si la génération continue d’échouer, examinez chaque erreur pour voir s’il existe des différences d’API entre le composant et le package NuGet.

<a name="require-update"></a>

## <a name="components-without-a-nuget-migration-path"></a>Composants sans chemin de migration NuGet

Ne vous inquiétez pas si vous ne trouvez pas immédiatement de remplacement pour les composants utilisés dans votre application. Les composants existants continueront à fonctionner dans Visual Studio 15,5 et le nœud **composants** s’affichera dans votre solution comme d’habitude.

Toutefois, les versions ultérieures de Visual Studio ne pourront _pas_ restaurer ou mettre à jour les composants.
Cela signifie que si vous ouvrez la solution sur un nouvel ordinateur, le composant ne sera pas téléchargé et installé. et l’auteur ne sera pas en mesure de vous fournir des mises à jour. Vous devez prévoir les éléments suivants :

- Extrayez les assemblys du composant et référencez-les directement dans votre projet.
- Contactez l’auteur du composant et demandez les plans à migrer vers NuGet.
- Examinez les autres packages NuGet ou recherchez le code source si le composant est open-source.

De nombreux fournisseurs de composants travaillent toujours sur la migration vers NuGet, tandis que d’autres (y compris les produits disponibles dans le commerce) peuvent rechercher d’autres options de remise.

## <a name="related-links"></a>Liens associés

- [Liste des plug-ins et des bibliothèques Xamarin populaires](https://github.com/xamarin/XamarinComponents/blob/master/README.md)
- [Installer et utiliser un package NuGet (Windows)](https://docs.microsoft.com/nuget/quickstart/use-a-package)
- [Inclusion d’un package NuGet (Mac)](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)
