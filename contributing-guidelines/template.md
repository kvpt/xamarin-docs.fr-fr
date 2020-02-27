---
title: Titre de cet article
description: Une description solide de cet article. Le contenu maintenu ici est ce que le système Microsoft Docs retourne avec les résultats de recherche, donc il doit s’agit d’une description complète de l’objectif et du contenu de ce fichier de document
keywords: keyword, list, for, internal, search
author: conceptdev
ms.author: crdun
ms.date: 02/26/2018
ms.topic: conceptual
ms.assetid: 11111111-2222-3333-4444-555555555555
ms.prod: xamarin
ms.openlocfilehash: f26a786f3da909c7d948fe99b4b3a1423352ecbe
ms.sourcegitcommit: ec62c7f28abc8e121656f1b93146657d90a4cab4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2020
ms.locfileid: "77553562"
---
# <a name="metadata-and-markdown-template"></a>Métadonnées et modèle Markdown

Ce modèle contient des exemples de syntaxe Markdown ainsi que des conseils sur la définition de métadonnées. Pour en tirer le meilleur parti, vous devez afficher à la fois le **Markdown brut** et la **vue rendue** (par exemple, le Markdown brut affiche le bloc de métadonnées contrairement à la vue rendue).

Quand vous créez un fichier Markdown, vous devez copier ce modèle dans un nouveau fichier, remplir les métadonnées comme indiqué ci-dessous, définir le titre de l’article comme titre H1 ci-dessus et supprimer le contenu.

## <a name="metadata"></a>Métadonnées

Le bloc de métadonnées complet, avec un ensemble d’annotations, est illustré ci-dessous :

```
---
title: This Article's Title
description: A good, solid description of this article. The content kept here is what the Microsoft Docs system returns with search results, so this had better be a complete description of the purpose and content for this document file
keywords: a, comma, separated, list, of keywords, to, use, for, this, doc, however, this, might, not, be, used, for, anything, much, anymore, but, still, good, to, have, if, you, want
author: conceptdev (your Github user name)
ms.author: crdun (your Microsoft Alias)
ms.date: 02/26/2018 (creation date, in US format: mm/dd/yyyy)
ms.topic: conceptual (one only of the following: conceptual, quickstart, tutorial, overview)
ms.assetid: b39854a6-c523-4a66-bef6-9b5da03bafff (a unique number representing the asset - just use a GUID, you can generate one at https://www.guidgenerator.com/)
ms.prod: xamarin (no reason to change this)
ms.custom: Analytics data, a field that gets imported into SkyEye so you can use it in custom reports
---
```

Quelques remarques importantes :

- Vous **devez** ajouter un espace entre le signe deux-points (:) et la valeur d’un élément de métadonnées.
- Si un élément de métadonnées facultatif n’a pas de valeur, supprimez-le (ne le laissez pas vide et n’utilisez pas « na »).
- Des signes deux-points dans une valeur (par exemple, un titre) interrompent l’analyseur de métadonnées. Dans ce cas, entourez le titre de guillemets doubles (par exemple, `title: "Writing .NET Core console apps: An advanced step-by-step guide"`).
- **title** : Ce titre s’affiche dans les résultats des moteurs de recherche. Le titre ne doit pas être identique au titre H1 et il doit contenir au maximum 60 caractères.
- **author**, **manager**, **ms.reviewer** : Le champ d’auteur doit contenir le **nom d’utilisateur GitHub** de l’auteur, et non son alias.  En revanche, les champs « manager » et « ms.reviewer » doivent contenir des alias Microsoft. ms.reviewer spécifie le nom du PM/développeur associé à l’article ou la fonctionnalité.
- **ms.assetid** : Il s’agit du GUID de l’article utilisé à des fins de suivi interne comme Business Intelligence (BI). Quand vous créez un fichier Markdown, récupérez un GUID sur [https://www.guidgenerator.com](https://www.guidgenerator.com).

## <a name="basic-markdown-gfm-and-special-characters"></a>Markdown de base, GFM et caractères spéciaux

Toute la syntaxe Markdown de base et GFM (GitHub Flavored Markdown) est prise en charge. Pour plus d’informations sur cette syntaxe, consultez :

- [Syntaxe Markdown de base](https://daringfireball.net/projects/markdown/syntax)
- [DocFX Flavored Markdown](https://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html)
- [Documentation GFM](https://guides.github.com/features/mastering-markdown)

Markdown utilise des caractères spéciaux tels que \*, \` et \# pour la mise en forme. Si vous souhaitez inclure l’un de ces caractères dans votre contenu, vous devez effectuer l’une des deux opérations suivantes :

- Placer une barre oblique inverse devant le caractère spécial pour l’« échappement » de ce caractère (par exemple, `\*` pour un \*)
- Utiliser le [code d’entité HTML](http://www.ascii.cl/htmlcodes.htm) pour le caractère (par exemple, `&#42;` pour un &#42;).

## <a name="file-name"></a>Nom de fichier

Les noms des fichiers utilisent les règles suivantes :

- Ils contiennent uniquement des lettres minuscules, des chiffres et des traits d’union.
- Aucun espace ni caractère de ponctuation. Utilisez les traits d’union pour séparer les mots et les nombres dans le nom de fichier.
- Utilisez des verbes d’actions spécifiques, tels que développer, acheter, générer, dépanner. Pas de substantif.
- Pas de mot trop court ; évitez un, et, le, en, ou, etc.
- Le nom doit être au format Markdown et utiliser l’extension de fichier .md.
- Privilégiez des noms de fichiers courts. Ils font partie de l’URL pour vos articles.

## <a name="headings"></a>Titres

Utilisez la mise en majuscules comme pour les phrases. Mettez toujours en majuscules :

- Le premier mot d’un titre.
- Le mot qui suit le signe deux-points dans un titre ou un en-tête (par exemple, « Comment : Trier un tableau »).

Les titres doivent être créés à l’aide du style atx, c’est-à-dire en utilisant 1 à 6 caractères de hachage (#) au début de la ligne pour indiquer un titre, correspondant aux niveaux de titres HTML H1 à H6. Des exemples de titres de premier et second niveau sont utilisés ci-dessus.

Il ne **doit** exister qu’un seul titre de premier niveau (H1) dans votre rubrique, qui sera affiché comme titre sur la page.

Si votre titre se termine par un caractère `#`, vous devez ajouter un autre caractère `#` à la fin pour que le titre soit correctement rendu. Par exemple, `# Async Programming in F# #`.

Les titres de second niveau génèrent la table des matières sur la page qui s’affiche dans la section « Dans cet article » en dessous du titre sur la page.

### <a name="third-level-heading"></a>Titre de troisième niveau
#### <a name="fourth-level-heading"></a>Titre de quatrième niveau
##### <a name="fifth-level-heading"></a>Titre de cinquième niveau
###### <a name="sixth-level-heading"></a>Titre de sixième niveau

## <a name="text-styling"></a>Style du texte

_Italique_ À utiliser pour les fichiers, dossiers, chemins (pour les éléments longs, séparés sur leur propre ligne), les nouveaux termes, les URL (sauf quand elles sont rendues sous forme de liens, par défaut).

**Gras** À utiliser pour les éléments d’interface utilisateur.

## <a name="links"></a>liens

### <a name="internal-links"></a>Liens internes

Pour établir un lien à un en-tête dans le même fichier Markdown (également appelés liens d’ancrage), vous devrez trouver l’ID de l’en-tête en question. Pour vérifier l’ID, affichez la source de l’article rendu, recherchez l’ID de l’en-tête (par exemple, `id="blockquote"`) et établissez le lien avec # + ID (par exemple, `#blockquote`).
L’ID est généré automatiquement en fonction du texte d’en-tête. Ainsi, par exemple, étant donné une section unique nommée `## Step 2`, l’ID ressemblerait à `id="step-2"`.

- Exemple : `[Chapter 1](#chapter-1)`

Pour établir un lien à un fichier Markdown dans le même dépôt, utilisez des [liens relatifs](https://www.w3.org/TR/WD-html40-970917/htmlweb.html#h-5.1.2), dont « .md » à la fin du nom de fichier.

- Exemple : `[Readme file](../readme.md)`
- Exemple : `[Welcome to .NET](../docs/welcome.md)`

Pour établir un lien à un en-tête d’un fichier Markdown dans le même dépôt, utilisez la liaison relative + la liaison hashtag.

- Exemple : `[.NET Community](../docs/welcome.md#community)`

### <a name="external-links"></a>Liens externes

Pour établir un lien à un fichier externe, utilisez l’URL complète comme lien.

- Exemple : `[GitHub](http://www.github.com)`

Si une URL apparaît dans un fichier Markdown, elle est transformée en lien interactif.

- Exemple : `http://www.github.com`

### <a name="links-to-apis"></a>Liens vers des API

Le système de génération a certaines extensions qui permettent d’établir des liens à des API .NET sans avoir à utiliser de liens externes.
Lors de la liaison à une API, vous pouvez utiliser son identificateur unique (UID) qui est généré automatiquement à partir du code source.

Vous pouvez utiliser l’une des syntaxes suivantes :

1. Lien Markdown : `[link_text](xref:UID)`
2. Lien automatique : `<xref:UID>`
3. Forme abrégée : `@UID`

- Exemple : `@System.String`
- Exemple : `[String class](xref:System.String)`

Pour plus d’informations sur l’utilisation de cette notation, consultez [Utilisation de renvois](https://dotnet.github.io/docfx/tutorial/links_and_cross_references.html#using-cross-reference).

> Pour l’instant, il n’existe aucun moyen facile de trouver les UID. La meilleure façon de trouver l’UID pour une API consiste à le rechercher dans ce dépôt : [docascode/coreapi](https://github.com/docascode/coreapi). Nous travaillons à la mise en place d’un meilleur système à l’avenir.

Quand l’UID contient les caractères spéciaux \` ou \#, la valeur de l’UID doit être encodée au format HTML (%60 et %23 respectivement), comme dans les exemples suivants :

- Exemple : @System.Threading.Tasks.Task\`1 devient `@System.Threading.Tasks.Task%601`
- Exemple : @System.Exception.\#ctor devient `@System.Exception.%23ctor`

## <a name="lists"></a>Listes

### <a name="ordered-lists"></a>Listes triées

1. This
1. Is
1. An
1. Ordered
1. List

#### <a name="ordered-list-with-an-embedded-list"></a>Liste triée avec une liste incorporée

1. Here
1. comes
1. an
1. embedded
    1. Miss Scarlett
    1. Professor Plum
1. ordered
1. list

### <a name="unordered-lists"></a>Listes non triées

- This
- is
- a
- bulleted
- list

#### <a name="unordered-list-with-an-embedded-list"></a>Liste non triée avec une liste incorporée

- This
- bulleted
- list
  - Mrs. Peacock
  - Mr. Green
- contains
- other
    1. Colonel Mustard
    1. Mrs. White
- lists

## <a name="horizontal-rule"></a>Ligne horizontale

---

## <a name="tables"></a>Tables

| Tables        | Sont           | Cool  |
| ------------- |:-------------:| -----:|
| col 3 est      | alignée à droite | 1 600 $ |
| col 2 est      | centrée      |   12 $ |
| col 1 est la valeur par défaut | alignée à gauche     |    1 $ |

Vous pouvez utiliser un [outil de génération de table Markdown](http://www.tablesgenerator.com/markdown_tables) pour les créer plus facilement.

### <a name="inline-code-blocks-with-language-identifier"></a>Blocs de code en ligne avec identificateur de langage

Utilisez trois accents graves (\`\`\`) + un ID de langage pour appliquer un codage en couleurs spécifique au langage à un bloc de code. Voici la liste complète des [ID de langages GFM](https://github.com/jmm/gfm-lang-ids/wiki/GitHub-Flavored-Markdown-(GFM)-language-IDs).

#### <a name="c9839"></a>C&#9839;

```c#
using System;
namespace HelloWorld
{
    class Hello
    {
        static void Main()
        {
            Console.WriteLine("Hello World!");

            // Keep the console window open in debug mode.
            Console.WriteLine("Press any key to exit.");
            Console.ReadKey();
        }
    }
}
```

#### <a name="xml"></a>xml

```xml
<dict>
    <key>CFBundleURLSchemes</key>
    <array>
        <string>evolveCountdown</string>
    </array>
```

#### <a name="xaml"></a>XAML

```xaml
<Label Text="Hello, XAML!"
        FontSize="Large"
        FontAttributes="Bold"
        TextColor="Blue" />
```

#### <a name="powershell"></a>PowerShell

```powershell
Clear-Host
$Directory = "C:\Windows\"
$Files = Get-Childitem $Directory -recurse -Include *.log `
-ErrorAction SilentlyContinue
```

### <a name="generic-code-block"></a>Bloc de code générique

Utilisez trois accents graves (&#96;&#96;&#96;) pour le codage de bloc de code générique.

> L’approche recommandée consiste à utiliser des blocs de code avec les identificateurs de langage, comme expliqué dans la section précédente, pour garantir la coloration syntaxique appropriée sur le site de documentation. Utilisez des blocs de code générique uniquement si nécessaire.

```
function fancyAlert(arg) {
    if(arg) {
        $.docs({div:'#foo'})
    }
}
```

### <a name="inline-code"></a>Code en ligne

Utilisez des accents graves (&#96;) pour `inline code`. Utilisez le code en ligne pour les commandes de ligne de commande, les noms de colonnes et de tables de base de données ainsi que les mots clés de langage.

## <a name="blockquotes"></a>Éléments blockquote

> La sécheresse durait maintenant depuis dix millions d’années et le règne des terribles lézards avait depuis longtemps pris fin. Ici, à l’équateur, sur le continent qui s’appellerait un jour l’Afrique, la lutte pour l’existence avait atteint un nouveau sommet dans la férocité, et le vainqueur n’était pas encore connu. Dans ce territoire aride et désolé, seul le plus petit, le plus rapide ou le plus puissant pouvait croître et espérer survivre.

## <a name="images"></a>Images

### <a name="static-image-or-animated-gif"></a>Image statique ou GIF animée

```
![this is the alt text](images/dotnet.png)
```

![Conception réactive](images/responsivedesign.gif)

### <a name="linked-image"></a>Image liée

```
[![alt text for linked image](images/dotnet.png)](https://dot.net)
```

## <a name="videos"></a>Vidéos

### <a name="channel-9"></a>Channel 9

Utilisez cette syntaxe blockquote spéciale :

```
> [!Video https://channel9.msdn.com/Shows/On-NET/Shipping-NET-Core-RC2--Tools-Preview-1/player]
```

L’URL de la vidéo de Channel 9 doit commencer par `https` et se terminer par `/player`, sinon le code incorpore la page entière au lieu de la vidéo uniquement.

### <a name="youtube"></a>YouTube

Utilisez cette syntaxe blockquote spéciale :

```
> [!Video https://youtube.com/embed/wXgnh2Q7Uv8]
```

La vidéo YouTube doit contenir `/embed/` (suivez le format ci-dessus), sinon elle tentera d’effectuer le rendu de la page entière et peut ne pas fonctionner.

## <a name="docsmicrosoft-extensions"></a>extensions docs.microsoft

docs.microsoft fournit quelques extensions supplémentaires à GitHub Flavored Markdown.

### <a name="alerts"></a>Alertes

Il est important d’utiliser les styles d’alerte suivants pour un rendu avec le style approprié sur le site de documentation. Toutefois, le moteur de rendu sur GitHub ne les différencie pas.

#### <a name="note"></a>Remarque

```
> [!NOTE]
> This is a NOTE
```

#### <a name="warning"></a>Warning

```
> [!WARNING]
> This is a WARNING
```

#### <a name="tip"></a>Conseil

```
> [!TIP]
> This is a TIP
```

#### <a name="important"></a>Important

```
> [!IMPORTANT]
> This is IMPORTANT
```

Et tous seront rendus comme ceci :

![Styles d’alertes](images/alerts.png)

### <a name="buttons"></a>Boutons

```
> [!div class="button"]
[button links](../docs/core/index.md)
```

Vous pouvez voir un exemple de boutons en action dans la [documentation Intune](https://docs.microsoft.com/intune/get-started/choose-how-to-enroll-devices).

### <a name="selectors"></a>Sélecteurs

```
> [!div class="op_single_selector"]
- [macOS](../docs/core/tutorials/using-on-macos.md)
- [Windows](../docs/core/tutorials/using-on-windows.md)
```

Vous pouvez voir un exemple de sélecteurs en action dans la [documentation Intune](https://docs.microsoft.com/intune/deploy-use/what-to-tell-your-end-users-about-using-microsoft-intune#how-your-end-users-get-their-apps).

### <a name="step-by-steps"></a>Procédures détaillées

```
> [!div class="step-by-step"]
[Pre](../docs/csharp/expression-trees-interpreting.md)
[Next](../docs/csharp/expression-trees-translating.md)
```

Vous pouvez voir un exemple de procédures détaillées en action dans la [documentation Advanced Threat Analytics](https://docs.microsoft.com/advanced-threat-analytics/deploy-use/install-ata-step2).
