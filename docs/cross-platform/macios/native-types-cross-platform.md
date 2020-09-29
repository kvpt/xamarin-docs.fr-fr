---
title: Utilisation de types natifs dans des applications multiplateformes
description: Cet article aborde l’utilisation des nouveaux types natifs iOS API unifiée (nint, nuint, nfloat) dans une application multiplateforme dans laquelle le code est partagé avec des appareils non-iOS tels que les systèmes d’exploitation Android ou Windows Phone.
ms.prod: xamarin
ms.assetid: B9C56C3B-E196-4ADA-A1DE-AC10D1001C2A
author: davidortinau
ms.author: daortin
ms.date: 04/07/2016
ms.openlocfilehash: 4819f8fc88a8a2c730fc25215e862d68cc5bd643
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457287"
---
# <a name="working-with-native-types-in-cross-platform-apps"></a>Utilisation de types natifs dans des applications multiplateformes

_Cet article aborde l’utilisation des nouveaux types natifs iOS API unifiée (nint, nuint, nfloat) dans une application multiplateforme dans laquelle le code est partagé avec des appareils non-iOS tels que les systèmes d’exploitation Android ou Windows Phone._

Les types natifs de types 64 fonctionnent avec les API iOS et Mac. Si vous écrivez du code partagé qui s’exécute également sur Android ou Windows, vous devez gérer la conversion des types unifiés en types .NET standard que vous pouvez partager.

Ce document décrit les différentes façons d’interagir avec le API unifiée à partir de votre code partagé/commun.

## <a name="when-to-use-the-native-types"></a>Quand utiliser les types natifs

Les API unifiées Xamarin. iOS et Xamarin. Mac incluent toujours les `int` types de données, et, ainsi que `uint` `float` les `RectangleF` `SizeF` types, et `PointF` . Ces types de données existants doivent continuer à être utilisés dans n’importe quel code multiplateforme partagé. Les nouveaux types de données natifs doivent uniquement être utilisés lors d’un appel à une API Mac ou iOS où la prise en charge des types orientés architecture est requise.

En fonction de la nature du code partagé, il peut arriver que du code multiplateforme doive gérer les `nint` `nuint` types de données, et `nfloat` . Par exemple : une bibliothèque qui gère des transformations sur des données rectangulaires qui utilisaient précédemment `System.Drawing.RectangleF` pour partager des fonctionnalités entre les versions Xamarin. iOS et Xamarin. Android d’une application doit être mise à jour pour gérer les types natifs sur iOS.

La manière dont ces modifications sont gérées dépend de la taille et de la complexité de l’application et de la forme de partage de code qui a été utilisée, comme nous le verrons dans les sections suivantes.

## <a name="code-sharing-considerations"></a>Considérations sur le partage de code

Comme indiqué dans le document [partage des options de code](~/cross-platform/app-fundamentals/code-sharing.md) , il existe deux façons principales de partager du code entre des projets multiplateformes : les projets partagés et les bibliothèques de classes portables. Les deux types ayant été utilisés, limitent les options dont nous disposons lors de la gestion des types de données natifs dans le code multiplateforme.

### <a name="portable-class-library-projects"></a>Projets de bibliothèque de classes portables

Une bibliothèque de classes portable (PCL) vous permet de cibler les plateformes que vous souhaitez prendre en charge, et d’utiliser des interfaces pour fournir des fonctionnalités spécifiques à la plateforme.

Étant donné que le type de projet PCL est compilé en un `.DLL` et qu’il n’a aucun sens de la API unifiée, vous serez obligé de continuer à utiliser les types de données existants ( `int` , `uint` , `float` ) dans le code source PCL et de caster les appels vers les classes et les méthodes de la bibliothèque PCL dans les applications frontales. Par exemple :

```csharp
using NativePCL;
...

CGRect rect = new CGRect (0, 0, 200, 200);
Console.WriteLine ("Rectangle Area: {0}", Transformations.CalculateArea ((RectangleF)rect));
```

### <a name="shared-projects"></a>Projets partagés

Le type de projet de ressource partagé vous permet d’organiser votre code source dans un projet distinct qui est ensuite inclus et compilé dans les applications frontales spécifiques à chaque plateforme, et d’utiliser les `#if` directives de compilateur nécessaires pour gérer les exigences spécifiques à la plateforme.

La taille et la complexité des applications mobiles frontales qui consomment du code partagé, ainsi que la taille et la complexité du code partagé, doivent être prises en compte lors du choix de la méthode de prise en charge des types de données natifs dans un projet de ressource partagée multiplateforme.

En fonction de ces facteurs, les types de solutions suivants peuvent être implémentés à l’aide des `if __UNIFIED__ ... #endif` directives du compilateur pour gérer le API unifiée modifications spécifiques du code.

#### <a name="using-duplicate-methods"></a>Utilisation de méthodes dupliquées

Prenons l’exemple d’une bibliothèque qui exécute des transformations sur des données rectangulaires fournies ci-dessus. Si la bibliothèque contient uniquement une ou deux méthodes très simples, vous pouvez choisir de créer des versions dupliquées de ces méthodes pour Xamarin. iOS et Xamarin. Android. Par exemple :

```csharp
using System;
using System.Drawing;

#if __UNIFIED__
using CoreGraphics;
#endif

namespace NativeShared
{
    public class Transformations
    {
        #region Constructors
        public Transformations ()
        {
        }
        #endregion

        #region Public Methods
        #if __UNIFIED__
            public static nfloat CalculateArea(CGRect rect) {

                // Calculate area...
                return (rect.Width * rect.Height);

            }
        #else
            public static float CalculateArea(RectangleF rect) {

                // Calculate area...
                return (rect.Width * rect.Height);

            }
        #endif
        #endregion
    }
}
```

Dans le code ci-dessus, étant donné que la `CalculateArea` routine est très simple, nous avons utilisé la compilation conditionnelle et créé une version distincte API unifiée de la méthode. En revanche, si la bibliothèque contenait de nombreuses routines ou plusieurs routines complexes, cette solution n’est pas réalisable, car elle présente un problème de synchronisation de toutes les méthodes synchronisées pour les modifications ou les correctifs de bogues.

#### <a name="using-method-overloads"></a>Utilisation de surcharges de méthode

Dans ce cas, la solution peut consister à créer une version de surcharge des méthodes à l’aide des types de données 32 bits afin qu’ils prennent maintenant `CGRect` comme paramètre et/ou une valeur de retour, convertir cette valeur en `RectangleF` (sachant que la conversion de `nfloat` en `float` est une conversion avec perte) et appeler la version d’origine de la routine pour effectuer le travail réel. Par exemple :

```csharp
using System;
using System.Drawing;

#if __UNIFIED__
using CoreGraphics;
#endif

namespace NativeShared
{
    public class Transformations
    {
        #region Constructors
        public Transformations ()
        {
        }
        #endregion

        #region Public Methods
        #if __UNIFIED__
            public static nfloat CalculateArea(CGRect rect) {

                // Call original routine to calculate area
                return (nfloat)CalculateArea((RectangleF)rect);

            }
        #endif

        public static float CalculateArea(RectangleF rect) {

            // Calculate area...
            return (rect.Width * rect.Height);

        }

        #endregion
    }
}

```

Là encore, il s’agit d’une bonne solution, à condition que la perte de précision n’affecte pas les résultats des besoins spécifiques de votre application.

#### <a name="using-alias-directives"></a>Utilisation de directives d’alias

Pour les zones où la perte de précision est un problème, une autre solution possible consiste à utiliser des `using` directives pour créer un alias pour les types de données natifs et coregraphics en incluant le code suivant au début des fichiers de code source partagés et en convertissant les `int` valeurs nécessaires, `uint` ou `float` en `nint` , `nuint` ou `nfloat` :

```csharp
#if __UNIFIED__
    // Mappings Unified CoreGraphic classes to MonoTouch classes
    using RectangleF = global::CoreGraphics.CGRect;
    using SizeF = global::CoreGraphics.CGSize;
    using PointF = global::CoreGraphics.CGPoint;
#else
    // Mappings Unified types to MonoTouch types
    using nfloat = global::System.Single;
    using nint = global::System.Int32;
    using nuint = global::System.UInt32;
#endif
```

Pour que notre exemple de code devienne :

```csharp
using System;
using System.Drawing;

#if __UNIFIED__
    // Map Unified CoreGraphic classes to MonoTouch classes
    using RectangleF = global::CoreGraphics.CGRect;
    using SizeF = global::CoreGraphics.CGSize;
    using PointF = global::CoreGraphics.CGPoint;
#else
    // Map Unified types to MonoTouch types
    using nfloat = global::System.Single;
    using nint = global::System.Int32;
    using nuint = global::System.UInt32;
#endif

namespace NativeShared
{

    public class Transformations
    {
        #region Constructors
        public Transformations ()
        {
        }
        #endregion

        #region Public Methods
        public static nfloat CalculateArea(RectangleF rect) {

            // Calculate area...
            return (rect.Width * rect.Height);

        }
        #endregion
    }
}
```

Notez que nous avons ici modifié la `CalculateArea` méthode pour retourner un à `nfloat` la place de la norme `float` . Cela a été fait afin de ne pas obtenir une erreur de compilation tentant de convertir _implicitement_ le `nfloat` résultat de notre calcul (puisque les deux valeurs multipliées sont de type `nfloat` ) dans une `float` valeur de retour.

Si le code est compilé et exécuté sur un appareil non API unifiée, le `using nfloat = global::System.Single;` mappe `nfloat` à un `Single` qui effectue une conversion implicite en `float` qui permet à l’application frontale consommatrice d’appeler la `CalculateArea` méthode sans modification.

#### <a name="using-type-conversions-in-the-front-end-app"></a>Utilisation des conversions de type dans l’application frontale

Si vos applications frontales ne font qu’un petit nombre d’appels à votre bibliothèque de code partagée, une autre solution consisterait à conserver la bibliothèque inchangée et à effectuer une conversion de type dans l’application Xamarin. iOS ou Xamarin. Mac lors de l’appel de la routine existante. Par exemple :

```csharp
using NativeShared;
...

CGRect rect = new CGRect (0, 0, 200, 200);
Console.WriteLine ("Rectangle Area: {0}", Transformations.CalculateArea ((RectangleF)rect));
```

Si l’application consommatrice effectue des centaines d’appels à la bibliothèque de code partagée, cela ne constitue pas une bonne solution.

En se basant sur l’architecture de notre application, nous pourrions utiliser une ou plusieurs des solutions ci-dessus pour prendre en charge les types de données natifs (si nécessaire) dans notre code multiplateforme.

## <a name="xamarinforms-applications"></a>Applications Xamarin. Forms

Les éléments suivants sont nécessaires pour utiliser Xamarin. Forms pour les interfaces multiplateformes qui seront également partagées avec une application API unifiée :

- La solution complète doit utiliser la version 1.3.1 (ou une version ultérieure) du package NuGet Xamarin. Forms.
- Pour tout rendu personnalisé Xamarin. iOS, utilisez les mêmes types de solutions présentées ci-dessus en fonction de la façon dont le code d’interface utilisateur a été partagé (projet partagé ou PCL).

Comme dans une application multiplateforme standard, les types de données 32 bits existants doivent être utilisés dans n’importe quel code multiplateforme partagé pour la plupart des situations. Les nouveaux types de données natifs doivent uniquement être utilisés lors d’un appel à une API Mac ou iOS où la prise en charge des types orientés architecture est requise.

Pour plus d’informations, consultez notre documentation sur la [mise à jour des applications Xamarin. Forms existantes](~/cross-platform/macios/unified/updating-xamarin-forms-apps.md) .

## <a name="summary"></a>Résumé

Dans cet article, nous avons vu quand utiliser les types de données natifs dans une application API unifiée et leurs implications interplateformes. Nous avons présenté plusieurs solutions qui peuvent être utilisées dans les situations où les nouveaux types de données natifs doivent être utilisés dans les bibliothèques multiplateformes. Nous avons également vu un guide rapide sur la prise en charge des API unifiées dans Xamarin. Forms applications multiplateformes.

## <a name="related-links"></a>Liens associés

- [API unifiée](~/cross-platform/macios/unified/index.md)
- [Types natifs](~/cross-platform/macios/nativetypes.md)
- [Options de partage de code](~/cross-platform/app-fundamentals/code-sharing.md)
- [Exemple de partage de code](/samples/xamarin/mobile-samples/sharingcode/)