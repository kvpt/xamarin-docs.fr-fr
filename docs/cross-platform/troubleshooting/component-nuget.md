---
title: La mise à jour des références de composants vers NuGet
description: Ce document explique comment remplacer vos références de composant avec les packages NuGet pour durable vos applications, étant donné que le Store du composant Xamarin a été abandonné.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 9E6C986F-3FBA-4599-8367-FB0C565C0ADE
author: asb3993
ms.author: amburns
ms.date: 04/18/2018
ms.openlocfilehash: fbef8f60a31511be926ee2adf5e239ad8955b208
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67864522"
---
# <a name="updating-component-references-to-nuget"></a>La mise à jour des références de composants vers NuGet

> [!IMPORTANT]
> Le composant Store a été suspendue à compter du 15 mai 2018 (cette fermeture a été initialement [annoncé](https://blog.xamarin.com/hello-nuget-new-home-xamarin-components/) en novembre 2017).
>
> Composants Xamarin ne sont plus prises en charge dans Visual Studio et doit être remplacés par les packages NuGet. Suivez les instructions ci-dessous pour supprimer manuellement les références de composant à partir de vos projets.

Reportez-vous à ces instructions pour l’ajout de packages NuGet sur [Windows](https://docs.microsoft.com/nuget/quickstart/use-a-package) ou [Mac](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough).

Une liste de Xamarin populaires [plug-ins et les bibliothèques](https://github.com/xamarin/XamarinComponents/blob/master/README.md) est disponible pour aider à trouver des alternatives aux composants qui ne sont pas disponibles dans les packages NuGet.

## <a name="manually-removing-component-references"></a>Suppression manuelle des références de composants

La version 15.6 de Visual Studio et la version 7.4 de Visual Studio pour Mac ne plus en charge les composants dans votre projet. 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Si vous chargez un projet dans Visual Studio, la boîte de dialogue suivante s’affiche, expliquant que vous devez supprimer tous les composants de votre projet manuellement :

![Boîte de dialogue expliquant qu’un composant a été trouvé dans votre projet et doit être supprimé d’alerte](component-nuget-images/component-alert-vs.png)

Pour supprimer un composant de votre projet :

1. Ouvrez le **.csproj** fichier. Pour ce faire, avec le bouton droit sur le nom du projet et sélectionnez **décharger le projet**. 

2. Clic droit sur le projet déchargé, puis sélectionnez **modifier {your-project-name} .csproj**.

3. Rechercher toutes les références dans le fichier à `XamarinComponentReference`. Il doit ressembler à l’exemple suivant :

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

4. Supprimer les références à `XamarinComponentReference` et enregistrez le fichier. Dans l’exemple ci-dessus, il est sécurisé supprimer l’intégralité de `ItemGroup`.

5. Une fois que le fichier a été enregistré, avec le bouton droit sur le nom du projet et sélectionnez **recharger le projet**.

6. Répétez les étapes ci-dessus pour chaque projet dans votre solution.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

Si vous chargez un projet dans Visual Studio pour Mac, la boîte de dialogue suivante s’affiche, expliquant que vous devez supprimer tous les composants de votre projet manuellement :

![Boîte de dialogue expliquant qu’un composant a été trouvé dans votre projet et doit être supprimé d’alerte](component-nuget-images/component-alert.png)

Pour supprimer un composant de votre projet :

1. Ouvrez le fichier .csproj. Pour ce faire, avec le bouton droit sur le nom du projet et sélectionnez **Outils > modifier le fichier**.

2. Rechercher toutes les références dans le fichier à `XamarinComponentReference`. Il doit ressembler à l’exemple suivant :

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

3. Supprimer les références à `XamarinComponentReference` et enregistrez le fichier. Dans l’exemple ci-dessus, il est sécurisé supprimer l’intégralité de `ItemGroup`

4. Répétez les étapes ci-dessus pour chaque projet dans votre solution.

-----

> [!WARNING]
> Les instructions suivantes fonctionnent uniquement avec les versions antérieures de Visual Studio.
> Le **composants** nœud n’est plus disponible dans les versions actuelles de Visual Studio 2017 ou Visual Studio pour Mac.

Les sections suivantes expliquent comment mettre à jour des solutions existantes de Xamarin pour modifier les références de composant pour les packages NuGet.

- [Composants qui contiennent les packages NuGet](#contain)
- [Composants avec les remplacements de NuGet](#replace)

La plupart des composants se répartissent dans les catégories ci-dessus.
Si vous utilisez un composant qui ne semble pas disposer d’un package NuGet équivalent, lire la [composants sans un chemin de migration NuGet](#require-update) section ci-dessous.

<a name="contain" />

## <a name="components-that-contain-nuget-packages"></a>Composants qui contiennent les packages NuGet

De nombreux composants contiennent déjà des packages NuGet, et le chemin de migration consiste simplement à supprimer la référence du composant.

Vous pouvez déterminer si le composant inclut déjà un package NuGet en double-cliquant sur le composant dans la solution :

![Nœud de composants développé](component-nuget-images/solution-sml.png)

Le **Packages** onglet répertorie tous les packages NuGet inclus dans le composant :

![Onglet packages contient NuGet](component-nuget-images/packages-tab-sml.png)

Notez que le **assemblys** onglet sera vide :

![Onglet assemblys est vide](component-nuget-images/assemblies-tab-empty-sml.png)

### <a name="updating-the-solution"></a>La mise à jour de la Solution

Pour mettre à jour votre solution, vous devez supprimer le **composant** entrée à partir de la solution :

![Supprimer le composant](component-nuget-images/delete-component-sml.png)

Le package NuGet restera répertorié dans le **Packages** nœud et votre application compilera et s’exécuter comme d’habitude. À l’avenir, les mises à jour ce package seront effectuées par le biais de la **Nuget** fonctionnalité de mise à jour :

![Mettre à jour de package NuGet](component-nuget-images/nuget-update-sml.png)


<a name="replace" />

## <a name="components-with-nuget-replacements"></a>Composants avec les remplacements de NuGet

Si la page d’informations de composant **assemblys** onglet contient des entrées comme indiqué ci-dessous, vous devez rechercher le package NuGet équivalent manuellement.

![Contient des assemblys](component-nuget-images/assemblies-tab-sml.png)

Notez que le **Packages** onglet sera probablement vide :

![](component-nuget-images/packages-tab-empty-sml.png)

_Il peut contenir des dépendances NuGet, mais vous pouvez les ignorer._

Pour confirmer le remplacement NuGet package existe, effectuez une recherche sur [NuGet.org](https://www.nuget.org/packages), en utilisant le nom de composant, vous pouvez aussi par auteur.

Par exemple, vous pouvez trouver ce fameux **sqlite-net-pcl** package en recherchant :

- [`sqlite-net-pcl`](https://www.nuget.org/packages?q=sqlite-net-pcl) – le nom du produit.
- [`praeclarum`](https://www.nuget.org/packages?q=praeclarum) – profil de l’auteur.

### <a name="updating-the-solution"></a>La mise à jour de la Solution

Une fois que vous avez confirmé que le composant est disponible dans NuGet, procédez comme suit :

#### <a name="delete-the-component"></a>Supprimer le composant

Cliquez avec le bouton droit sur le composant dans la solution et choisissez **supprimer**:

![Supprimer le composant](component-nuget-images/remove-component-sml.png)

Cette opération supprimera le composant et toutes les références. Cela arrêtera votre build, jusqu'à ce que vous ajoutez le package NuGet équivalent pour le remplacer.

#### <a name="add-the-nuget-package"></a>Ajoutez le package NuGet

1. Avec le bouton droit sur le **Packages** nœud et choisissez **ajouter des Packages...** .
2. Recherchez le remplacement de NuGet par nom ou l’auteur :

    ![](component-nuget-images/nuget-search-sml.png)

3. Appuyez sur **ajouter Package**.

Le package NuGet sera ajouté à votre projet, ainsi que toutes les dépendances.
Cela devrait corriger la build. Si la génération continue d’échouer, examinez chaque erreur pour voir si des différences d’API entre le composant et le package NuGet.

<a name="require-update" />

## <a name="components-without-a-nuget-migration-path"></a>Composants sans un chemin de migration de NuGet

Ne doit pas si vous ne trouvez pas immédiatement un remplacement pour les composants utilisés dans votre application. Les composants existants continueront à fonctionner dans Visual Studio 15.5 et le **composants** nœud apparaîtra dans votre solution comme d’habitude.

Les versions futures de Visual Studio, toutefois, seront _pas_ restaurer ou mettre à jour des composants.
Cela signifie que si vous ouvrez la solution sur un nouvel ordinateur, le composant ne sera pas téléchargé et installé ; et l’auteur ne seront pas en mesure de vous fournir des mises à jour. Vous devez planifier :

* Extraire les assemblys à partir du composant et les référencer directement dans votre projet.
* Contactez l’auteur du composant et demandez-lui sur les plans de migration vers NuGet.
* Examiner les autres packages NuGet, ou rechercher le code source si le composant est open source.

De nombreux fournisseurs de composant travaillez toujours sur la migration vers NuGet, et d’autres utilisateurs (y compris les produits disponibles dans le commerce) peuvent être examen des options de livraison de remplacement.


## <a name="related-links"></a>Liens associés
- [Liste de Xamarin Plugins et de bibliothèques populaires](https://github.com/xamarin/XamarinComponents/blob/master/README.md)
- [Installer et utiliser un package NuGet (Windows)](https://docs.microsoft.com/nuget/quickstart/use-a-package)
- [Inclusion d’un package NuGet (Mac)](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)
