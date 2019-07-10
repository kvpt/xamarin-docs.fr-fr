---
title: Conception de l’API Xamarin.iOS
description: Ce document décrit certains des principes directeurs qui permettant de concevoir les APIs Xamarin.iOS et comment ils concernent les Objective-C.
ms.prod: xamarin
ms.assetid: 322D2724-AF27-6FFE-BD21-AA1CFE8C0545
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 5417050daa84197dd7dc2a270ae054a3a73fd3bc
ms.sourcegitcommit: 58d8bbc19ead3eb535fb8248710d93ba0892e05d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67675080"
---
# <a name="xamarinios-api-design"></a>Conception de l’API Xamarin.iOS

Outre la base des bibliothèques de classes de Base qui font partie de Mono, [Xamarin.iOS](http://www.xamarin.com/iOS) est livré avec des liaisons pour iOS diverses API pour permettre aux développeurs de créer des applications iOS natives avec Mono.

Au cœur de Xamarin.iOS, il existe un interopérabilité moteur qui établit un pont entre le monde c# avec le monde de Objective-C, ainsi que les liaisons pour l’API C comme CoreGraphics iOS et [OpenGL ES](#OpenGLES).

Le runtime de bas niveau pour communiquer avec le code Objective-C est dans [MonoTouch.ObjCRuntime](#MonoTouch.ObjCRuntime). En haut de cette option, les liaisons pour [Foundation](#MonoTouch.Foundation), CoreFoundation, et [UIKit](#MonoTouch.UIKit) sont fournies.

## <a name="design-principles"></a>Principes de conception

Voici quelques-unes de nos principes de conception pour les liaisons Xamarin.iOS (ce qu’ils s’appliquent également à Xamarin.Mac, les liaisons Mono pour Objective-C sur macOS) :

- Suivez le [instructions de conception d’infrastructure](https://docs.microsoft.com/dotnet/standard/design-guidelines)
- Permettre aux développeurs de classes de sous-classe Objective-C :

  - Dériver à partir d’une classe existante
  - Appelez le constructeur de base pour la chaîne
  - Méthodes de substitution doit être effectuée avec C#de remplacer le système
  - Sous-classement doit fonctionner avec les constructions c# standards

- N’exposez pas aux développeurs de sélecteurs Objective-C
- Fournir un mécanisme pour appeler des bibliothèques Objective-C arbitraires
- Effectuer des tâches courantes Objective-C simple et compliquée possible de tâches Objective-C
- Exposer les propriétés de Objective-C en tant que propriétés c#
- Exposer une API fortement typée :

  - Augmenter la sécurité de type
  - Réduire les erreurs d’exécution
  - Obtenir de l’IDE IntelliSense sur les types de retour
  - Permet de documentation de fenêtre contextuelle IDE

- Encourager l’exploration dans l’IDE de l’API :

  - Par exemple, au lieu d’exposer un tableau faiblement typé comme suit :
    
    ```objc
    NSArray *getViews
    ```
    Exposer un type fort, comme suit :
    
    ```csharp
    NSView [] Views { get; set; }
    ```
    
    Cela permet de Visual Studio pour Mac pour effectuer la saisie semi-automatique lors de l’exploration de l’API, rend tout le `System.Array` opérations disponibles sur la valeur retournée, ainsi que de la valeur de retour de participer à LINQ.

- Types c# natifs :

  - [`NSString` devient `string`](~/ios/internals/api-design/nsstring.md)
  - Activer `int` et `uint` paramètres qui aurait dû être énumérations dans les énumérations c# et énumérations c# avec `[Flags]` attributs
  - Au lieu de type indépendant `NSArray` objets, exposent des tableaux en tant que tableaux fortement typée.
  - Pour les événements et les notifications, offrir aux utilisateurs un choix entre :

    - Une version fortement typée par défaut
    - Une version faiblement typé pour les cas d’utilisation avancée

- Prend en charge le modèle de délégué Objective-C :

    - Système d’événement c#
    - Exposer les délégués c# (expressions lambda, méthodes anonymes, et `System.Delegate`) aux API Objective-C sous forme de blocs

### <a name="assemblies"></a>Assemblys

Xamarin.iOS inclut un nombre d’assemblys qui constituent le *Xamarin.iOS profil*. Le [assemblys](~/cross-platform/internals/available-assemblies.md) page comporte des informations supplémentaires.

### <a name="major-namespaces"></a>Espaces de noms de principaux 

<a name="MonoTouch.ObjCRuntime" />

#### <a name="objcruntime"></a>ObjCRuntime

Le [ObjCRuntime](xref:ObjCRuntime) espace de noms permet aux développeurs de combler les mondes entre c# et Objective-C.
Il s’agit d’une nouvelle liaison, spécifiquement conçue pour iOS, basées sur l’expérience de Cocoa # et Gtk #.

<a name="MonoTouch.Foundation" />

#### <a name="foundation"></a>Foundation

Le [Foundation](xref:Foundation) espace de noms fournit les types de base de données conçu pour interagir avec l’infrastructure Objective-C Foundation qui fait partie du iOS et elle est la base de la programmation orientée objet dans Objective-C.

Xamarin.iOS reflète en c#, la hiérarchie de classes à partir d’Objective-C. Par exemple, la classe de base Objective-C [NSObject](https://developer.apple.com/iphone/library/documentation/Cocoa/Reference/Foundation/Classes/NSObject_Class/Reference/Reference.html) est utilisable à partir de c# via [Foundation.NSObject](xref:Foundation.NSObject).

Bien que cet espace de noms fournit des liaisons pour les types de Objective-C Foundation sous-jacente, dans certains cas, nous avons mappé les types sous-jacents aux types .NET. Exemple :

- Au lieu de traiter [chaîne NSString](https://developer.apple.com/iphone/library/documentation/Cocoa/Reference/Foundation/Classes/NSString_Class/Reference/NSString.html) et [NSArray](https://developer.apple.com/library/ios/#documentation/Cocoa/Reference/Foundation/Classes/NSArray_Class/NSArray.html), le runtime expose ceux-ci comme c# [chaîne](xref:System.String)s et fortement typées [tableau](xref:System.Array)s tout au long l’API.

- API d’assistance différentes sont exposés ici pour permettre aux développeurs lier le tiers Objective-C, API, autres iOS API ou les API qui ne sont pas liés par Xamarin.iOS.

Pour plus d’informations sur la liaison d’API, consultez le [Générateur de liaison Xamarin.iOS](~/cross-platform/macios/binding/binding-types-reference.md) section.


##### <a name="nsobject"></a>NSObject

Le [NSObject](xref:Foundation.NSObject) type constitue le fondement de toutes les liaisons Objective-C. Types de Xamarin.iOS mettre en miroir de deux classes de types à partir de l’iOS CocoaTouch APIs : les types C (généralement appelés types de CoreFoundation) et les types de Objective-C (ils dérivent toutes de la classe NSObject).

Pour chaque type qui reflète un type non managé, il est possible d’obtenir l’objet natif via le [gérer](xref:Foundation.NSObject.Handle) propriété.

Bien que Mono fournira le garbage collection pour tous vos objets, le `Foundation.NSObject` implémente le [System.IDisposable](xref:System.IDisposable) interface. Cela signifie que vous pouvez libérer explicitement les ressources de n’importe quel NSObject donné sans avoir à attendre le Garbage Collector kick dans. Ceci est important lorsque vous utilisez NSObjects importante, par exemple, les UIImages qui peut contenir des pointeurs de grands blocs de données.

Si votre type doit exécuter la finalisation déterministe, substituez le [NSObject.Dispose(bool) méthode](xref:Foundation.NSObject.Dispose(System.Boolean)) le paramètre à la méthode Dispose est « bool disposing », et si défini sur true elle signifie que votre méthode Dispose est appelée, car l’utilisateur appelés explicitement (suppression) sur l’objet. Si la valeur est false, cela signifie que votre méthode Dispose (bool disposing) est appelée à partir du finaliseur sur le thread finaliseur.


##### <a name="categories"></a>Categories

En commençant avec Xamarin.iOS 8.10, il est possible de créer des catégories d’Objective-C à partir de c#.

Cette opération est effectuée à l’aide de la `Category` attribut, en spécifiant le type à étendre en tant qu’argument à l’attribut. L’exemple suivant étend par exemple chaîne NSString.

    [Category (typeof (NSString))]

Chaque méthode de catégorie à l’aide du mécanisme normal pour l’exportation des méthodes vers Objective-C à l’aide du `Export` attribut :

    [Export ("today")]
    public static string Today ()
    {
        return "Today";
    }

Toutes les méthodes d’extension managée doivent être statiques, mais il est possible de créer des méthodes d’instance Objective-C à l’aide de la syntaxe standard pour les méthodes d’extension dans c# :

    [Export ("toUpper")]
    public static string ToUpper (this NSString self)
    {
        return self.ToString ().ToUpper ();
    }

et le premier argument de la méthode d’extension sera l’instance sur laquelle la méthode a été appelée.

Exemple complet :

```csharp
[Category (typeof (NSString))]
public static class MyStringCategory
{
    [Export ("toUpper")]
    static string ToUpper (this NSString self)
    {
        return self.ToString ().ToUpper ();
    }
}
```

Cet exemple ajoute une méthode d’instance toUpper native à la classe de chaîne NSString, ce qui peut être appelée à partir d’Objective-C.

```csharp
[Category (typeof (UIViewController))]
public static class MyViewControllerCategory
{
    [Export ("shouldAudoRotate")]
    static bool GlobalRotate ()
    {
        return true;
    }
}
```

Un scénario où cela est utile est Ajout d’une méthode à un ensemble de classes dans votre code base, par exemple, vous pouvez ainsi vous tous les `UIViewController` instances signalent qu’ils peuvent en rotation :

```csharp
[Category (typeof (UINavigationController))]
class Rotation_IOS6 {
      [Export ("shouldAutorotate:")]
      static bool ShouldAutoRotate (this UINavigationController self)
      {
          return true;
      }
}
```


##### <a name="preserveattribute"></a>PreserveAttribute

PreserveAttribute est un attribut personnalisé qui est utilisé pour indiquer mtouch : l’outil de déploiement de Xamarin.iOS – pour conserver un type ou un membre d’un type, pendant la phase de traitée lorsque l’application afin de réduire sa taille.

Chaque membre qui n’est pas lié de manière statique par l’application est soumis à suppression. Par conséquent, cet attribut est utilisé pour marquer les membres qui ne sont pas statiquement référencés, mais qui sont toujours requis par votre application.

Par exemple, si vous instanciez dynamiquement des types, vous souhaiterez éventuellement conserver le constructeur par défaut de vos types. Si vous utilisez la sérialisation XML, vous souhaiterez éventuellement conserver les propriétés de vos types.

Vous pouvez appliquer cet attribut à chaque membre d’un type ou au type lui-même. Si vous souhaitez conserver l’ensemble du type, vous pouvez utiliser la syntaxe [conserver (AllMembers = true)] sur le type.

<a name="MonoTouch.UIKit" />

#### <a name="uikit"></a>UIKit

Le [UIKit](xref:UIKit) espace de noms contient un mappage pour tous les composants d’interface utilisateur qui composent CocoaTouch sous la forme de classes c#. L’API a été modifié pour suivre les conventions utilisées dans le langage c#.

Délégués c# sont fournies pour les opérations courantes. Consultez le [délégués](#Delegates) section pour plus d’informations.

<a name="OpenGLES" />

#### <a name="opengles"></a>OpenGLES

Pour OpenGLES, nous distribuer un [modifié version](xref:OpenTK) de la [OpenTK](http://www.opentk.com/) API, une liaison et orienté objet pour OpenGL qui a été modifiée pour utiliser les types de données CoreGraphics et structures, mais aussi exposer uniquement les fonctionnalités qui sont disponibles sur iOS.

La fonctionnalité OpenGLES 1.1 est disponible via le type de ES11.GL documenté [ici](xref:OpenTK.Graphics.ES11.GL) type.

La fonctionnalité OpenGLES 2.0 est disponible via le type de ES20.GL documenté [ici](xref:OpenTK.Graphics.ES20.GL) type.

La fonctionnalité OpenGLES 3.0 est disponible via le type de ES30.GL documenté [ici](xref:OpenTK.Graphics.ES30.GL) type.


### <a name="binding-design"></a>Conception de liaison

Xamarin.iOS n’est pas simplement une liaison à la plateforme Objective-C sous-jacente. Il étend le système de type .NET et le système de répartition pour une meilleure blend c# et Objective-C.

Tout comme les P/Invoke est un outil utile pour appeler des bibliothèques natives sur Windows et Linux, ou en tant que IJW prise en charge peut être utilisé pour l’interopérabilité COM sur Windows, Xamarin.iOS étend le runtime pour prendre en charge les objets c# de liaison aux objets d’Objective-C.

La discussion dans les prochains quelques sections suivantes n’est pas nécessaire pour les utilisateurs qui créent des applications Xamarin.iOS, mais qui aideront les développeurs à comprendre comment les choses sont effectuées et l’aidera lors de la création d’applications plus complexes.



#### <a name="types"></a>Types

Où il paraissait logique, les types c# sont exposées au lieu de bas niveau types Foundation, à l’univers c#.  Cela signifie que [l’API utilise le type « string » c# au lieu de la chaîne NSString](~/ios/internals/api-design/nsstring.md) et il utilise des tableaux C# fortement typées au lieu d’exposer NSArray.

En règle générale, dans la conception de Xamarin.iOS et Xamarin.Mac, sous-jacent `NSArray` objet n’est pas exposé. Au lieu de cela, le runtime convertit automatiquement `NSArray`s aux tableaux fortement typées de certaines `NSObject` classe. Par conséquent, Xamarin.iOS n’expose pas une méthode faiblement typée comme GetViews pour retourner une NSArray :

```csharp
NSArray GetViews ();
```

Au lieu de cela, la liaison expose une valeur de retour fortement typée, comme suit :

```csharp
UIView [] GetViews ();
```

Voici quelques-unes des méthodes exposées dans `NSArray`, pour les cas extrêmes où vous souhaiterez utiliser un `NSArray` directement, mais leur utilisation est déconseillée dans la liaison de l’API.

En outre, dans le **API classique** au lieu d’exposer `CGRect`, `CGPoint` et `CGSize` à partir de l’API CoreGraphics, nous avons remplacé ceux dont le `System.Drawing` implémentations `RectangleF`, `PointF`et `SizeF` car elles seraient aident les développeurs conservent code OpenGL existant qui utilise OpenTK. Lorsque vous utilisez le nouveau 64-bit **API unifiée**, l’API de CoreGraphics doit être utilisée.

<a name="Inheritance" />

#### <a name="inheritance"></a>Héritage

La conception de l’API Xamarin.iOS permet aux développeurs d’étendre les types Objective-C natifs de la même façon qu’ils seraient d’étendre un type c#, à l’aide du mot clé « override » sur une classe dérivée, ainsi que le chaînage l’implémentation de base à l’aide du mot clé c# « base ».

Cette conception permet aux développeurs d’éviter d’avoir avec sélecteurs Objective-C dans le cadre de leur processus de développement, car l’ensemble du système Objective-C est déjà encapsulé dans les bibliothèques de Xamarin.iOS.


#### <a name="types-and-interface-builder"></a>Types et Interface Builder

Lorsque vous créez des classes .NET qui sont des instances de types créés par Interface Builder, vous devez fournir un constructeur qui accepte un seul `IntPtr` paramètre.
Cela est nécessaire pour lier l’instance d’objet managé avec l’objet non managé.
Le code se compose d’une seule ligne, comme suit :

```csharp
public partial class void MyView : UIView {
   // This is the constructor that you need to add.
   public MyView (IntPtr handle) : base (handle) {}
}
```

<a name="Delegates" />


#### <a name="delegates"></a>Délégués

Objective-C et c# ont des significations différentes pour le délégué de word dans chaque langue.

Dans le monde Objective-C et dans la documentation que vous rencontrerez en ligne sur CocoaTouch, un délégué est généralement une instance d’une classe qui répondra à un ensemble de méthodes. Cela est très similaire à une interface c#, avec la différence étant que les méthodes ne sont pas toujours obligatoires.

Ces délégués jouent un rôle important dans UIKit APIs et d’autres CocoaTouch. Ils sont utilisés pour effectuer diverses tâches :

-  Fournir des notifications à votre code (semblable à la remise des événements en c# ou Gtk +).
-  Pour implémenter des modèles pour les contrôles de visualisation de données.
-  Pour piloter le comportement d’un contrôle.


Le modèle de programmation a été conçu pour réduire la création de classes dérivées de modifier le comportement d’un contrôle. Cette solution est même esprit que les autres kits de ressources de l’interface graphique utilisateur ont réalisée au fil des années : GTK signaux, les emplacements Qt, événements de Winforms, WPF/Silverlight et ainsi de suite. Pour éviter d’avoir des centaines d’interfaces (une pour chaque action) ou de demander aux développeurs d’implémenter des méthodes trop que n’a pas besoin, Objective-C prend en charge les définitions de méthode facultative. Cela est différent de celui d’interfaces c# qui nécessitent toutes les méthodes à implémenter.

Dans les classes Objective-C, vous verrez que les classes qui utilisent ce modèle de programmation exposent une propriété, généralement appelée `delegate`, qui est nécessaire pour implémenter les composants obligatoires de l’interface et les composants de zéro ou plus, de l’élément facultatif.

Dans Xamarin.iOS trois mécanismes qui s’excluent mutuellement pour lier à ces délégués sont proposées :

1.  [Via les événements](#Via_Events).
2.  [Fortement typé via un `Delegate` propriété](#StrongDelegate)
3.  [Faiblement typé un `WeakDelegate` propriété](#WeakDelegate)

Par exemple, considérez le [UIWebView](https://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebView_Class/Reference/Reference.html) classe. Il distribue à un [UIWebViewDelegate](https://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebViewDelegate_Protocol/Reference/Reference.html) instance, ce qui est affectée à la [déléguer](https://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebView_Class/Reference/Reference.html#//apple_ref/occ/instp/UIWebView/delegate) propriété.

<a name="Via_Events" />

##### <a name="via-events"></a>Via les événements

De nombreux types, Xamarin.iOS crée automatiquement un délégué approprié qui transmettre les `UIWebViewDelegate` appels sur les événements c#. Pour `UIWebView`:

-  Le [webViewDidStartLoad](https://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebViewDelegate_Protocol/Reference/Reference.html#//apple_ref/occ/intfm/UIWebViewDelegate/webViewDidStartLoad:) méthode est mappée à la [UIWebView.LoadStarted](xref:UIKit.UIWebView.LoadStarted) événement.
-  Le [webViewDidFinishLoad](https://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebViewDelegate_Protocol/Reference/Reference.html#//apple_ref/occ/intfm/UIWebViewDelegate/webViewDidFinishLoad:) méthode est mappée à la [UIWebView.LoadFinished](xref:UIKit.UIWebView.LoadFinished) événement.
-  Le [webView:didFailLoadWithError](https://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebViewDelegate_Protocol/Reference/Reference.html#//apple_ref/occ/intfm/UIWebViewDelegate/webView:didFailLoadWithError:) méthode est mappée à la [UIWebView.LoadError](xref:UIKit.UIWebView.LoadError) événement.

Par exemple, ce programme simple enregistre les heures de début et de fin lorsque le chargement d’un site web d’afficher :

```csharp
DateTime startTime, endTime;
var web = new UIWebView (new CGRect (0, 0, 200, 200));
web.LoadStarted += (o, e) => startTime = DateTime.Now;
web.LoadFinished += (o, e) => endTime = DateTime.Now;
```


##### <a name="via-properties"></a>Par le biais de propriétés

Les événements sont utiles lorsqu’il peut y avoir plusieurs abonnés à l’événement. En outre, les événements sont limités aux cas où il n’existe aucune valeur de retour à partir du code.

Pour les cas où le code est censé renvoyer une valeur, nous avons choisi à la place pour les propriétés. Cela signifie qu’une seule méthode peut être définie à un moment donné dans un objet.

Par exemple, vous pouvez utiliser ce mécanisme pour faire disparaître le clavier à l’écran sur le gestionnaire pour un `UITextField`:

```csharp
void SetupTextField (UITextField tf)
{
    tf.ShouldReturn = delegate (textfield) {
        textfield.ResignFirstResponder ();
        return true;
    }
}
```

Le `UITextField`de `ShouldReturn` propriété dans ce cas prend comme argument un délégué qui retourne une valeur bool et détermine si le champ de texte doit faire quelque chose avec le comportement du bouton retour. Dans notre méthode, nous renvoyons *true* à l’appelant, mais nous supprimons également le clavier à partir de l’écran (cela se produit lorsque le champ de texte appelle `ResignFirstResponder`).

<a name="StrongDelegate"/>

##### <a name="strongly-typed-via-a-delegate-property"></a>Fortement typé via une propriété de délégué

Si vous préférez ne pas d’utiliser des événements, vous pouvez fournir votre propre [UIWebViewDelegate](xref:UIKit.UIWebViewDelegate) sous-classe et assignez-la à la [UIWebView.Delegate](xref:UIKit.UIWebView.Delegate) propriété. Une fois que UIWebView.Delegate a été affectée, le mécanisme de dispatch d’événement UIWebView ne fonctionneront plus et les méthodes UIWebViewDelegate seront appelés lorsque les événements correspondants se produisent.

Par exemple, ce type simple enregistre le temps que nécessaire pour charger un affichage web :

```csharp
class Notifier : UIWebViewDelegate  {
    DateTime startTime, endTime;

    public override LoadStarted (UIWebView webview)
    {
        startTime = DateTime.Now;
    }

    public override LoadingFinished (UIWebView webView)
    {
        endTime= DateTime.Now;
    }
}
```

La méthode ci-dessus est utilisée dans le code comme suit :

```csharp
var web = new UIWebView (new CGRect (0, 0, 200, 200));
web.Delegate = new Notifier ();
```

Créent un UIWebViewer et il sera lui demander d’envoyer des messages à une instance de notification, une classe que nous avons créé pour répondre aux messages.

Ce modèle est également utilisé pour contrôler le comportement de certains contrôles, par exemple dans le cas UIWebView, le [UIWebView.ShouldStartLoad](xref:UIKit.UIWebView.ShouldStartLoad) propriété permet la `UIWebView` instance au contrôle si le `UIWebView` chargera un page ou non.

Le modèle est également utilisé pour fournir les données à la demande pour quelques contrôles. Par exemple, le [UITableView](xref:UIKit.UITableView) contrôle est un puissant contrôle de rendu de la table – et l’apparence et le contenu est dues à une instance d’un [UITableViewDataSource](xref:UIKit.UITableViewDataSource)

<a name="WeakDelegate"/>

### <a name="loosely-typed-via-the-weakdelegate-property"></a>Faiblement typé via la propriété WeakDelegate

Outre la propriété fortement typée, il existe également un délégué typé faible qui permet au développeur de lier les choses différemment si vous le souhaitez.
Everywhere fortement typé `Delegate` est exposée dans la liaison de Xamarin.iOS, correspondante `WeakDelegate` propriété est également exposée.

Lorsque vous utilisez le `WeakDelegate`, vous êtes responsable de correctement décorant votre classe à l’aide la [exporter](xref:Foundation.ExportAttribute) attribut pour spécifier le sélecteur. Exemple :

```csharp
class Notifier : NSObject  {
    DateTime startTime, endTime;

    [Export ("webViewDidStartLoad:")]
    public void LoadStarted (UIWebView webview)
    {
        startTime = DateTime.Now;
    }

    [Export ("webViewDidFinishLoad:")]
    public void LoadingFinished (UIWebView webView)
    {
        endTime= DateTime.Now;
    }
}

[...]

var web = new UIWebView (new CGRect (0, 0, 200, 200));
web.WeakDelegate = new Notifier ();
```

Notez qu’une fois le `WeakDelegate` propriété a été affectée, le `Delegate` propriété ne sera pas utilisée. En outre, si vous implémentez la méthode dans une classe de base héritée que vous souhaitez [exportation], vous devez le rendre une méthode publique.


## <a name="mapping-of-the-objective-c-delegate-pattern-to-c35"></a>Mappage du modèle de délégué Objective-C et C&#35;

Lorsque vous consultez des exemples de Objective-C qui ressemblent à ceci :

```csharp
foo.delegate = [[SomethingDelegate] alloc] init]
```

Cela indique la langue pour créer et construire une instance de la classe « SomethingDelegate » et affectez la valeur à la propriété de délégué sur la variable de foo. Ce mécanisme est pris en charge par Xamarin.iOS, et la syntaxe c# est :

```csharp
foo.Delegate = new SomethingDelegate ();
```

Dans Xamarin.iOS nous vous proposons des classes fortement typées qui correspondent à la Objective-C des classes déléguées. Pour ce faire, vous devez sous-classement et substituer les méthodes définies par la mise en œuvre de Xamarin.iOS. Pour plus d’informations sur leur fonctionnement, consultez les section « modèles » ci-dessous.


##### <a name="mapping-delegates-to-c35"></a>Mappage des délégués à C&#35;

UIKit utilise en général les délégués Objective-C sous deux formes.

Le premier formulaire fournit une interface pour les modèles d’un composant. Par exemple, comme un mécanisme pour fournir des données à la demande pour une vue, telles que la fonctionnalité de stockage de données pour un affichage de liste.  Dans ce cas, vous devez toujours créer une instance de la classe appropriée et affectez la variable.

Dans l’exemple suivant, nous fournissons le `UIPickerView` avec une implémentation d’un modèle qui utilise des chaînes :

```csharp
public class SampleTitleModel : UIPickerViewTitleModel {

    public override string TitleForRow (UIPickerView picker, nint row, nint component)
    {
        return String.Format ("At {0} {1}", row, component);
    }
}

[...]

pickerView.Model = new MyPickerModel ();
```

La deuxième forme consiste à fournir une notification pour les événements. Dans ce cas, bien que nous exposons toujours l’API sous la forme décrite ci-dessus, nous fournissons également événements c#, ce qui doivent être plus simple à utiliser pour les opérations rapides et intégrée avec les délégués anonymes et expressions lambda en c#.

Par exemple, vous pouvez vous abonner à `UIAccelerometer` événements :

```csharp
UIAccelerometer.SharedAccelerometer.Acceleration += (sender, args) => {
   UIAcceleration acc = args.Acceleration;
   Console.WriteLine ("Time={0} at {1},{2},{3}", acc.Time, acc.X, acc.Y, acc.Z);
}
```

Les deux options sont disponibles où ils sens, mais en tant que programmeur, vous devez choisir une ou l’autre. Si vous créez votre propre instance d’un répondeur/délégué fortement typé et lui donner, il se peut que les événements c# ne sera pas fonctionnelles. Si vous utilisez les événements c#, les méthodes dans votre classe de répondeur/délégué ne seront jamais appelées.

L’exemple précédent utilisé `UIWebView` peuvent être écrites à l’aide d’expressions lambda de c# 3.0 comme suit :

```csharp
var web = new UIWebView (new CGRect (0, 0, 200, 200));
web.LoadStarted += () => { startTime = DateTime.Now; }
web.LoadFinished += () => { endTime = DateTime.Now; }
```


#### <a name="responding-to-events"></a>Réponse aux événements

Dans le code Objective-C, parfois, les gestionnaires d’événements pour plusieurs contrôles et les fournisseurs d’informations pour plusieurs contrôles, sera hébergée dans la même classe. Cela est possible, car les classes de répondent aux messages, et tant que classes répondent aux messages, il est possible de lier les objets.

Comme indiqué précédemment, Xamarin.iOS prend en charge les deux le c# basé sur événement modèle de programmation, et le modèle de délégué Objective-C, où vous pouvez créer une nouvelle classe qui implémente le délégué et substitue les méthodes souhaitées.

Il est également possible prendre en charge le modèle de d’Objective-C où répondeurs pour plusieurs opérations différents sont hébergés dans la même instance d’une classe. Pour ce faire Cependant, vous devrez utiliser les fonctionnalités de bas niveau de la liaison Xamarin.iOS.

Par exemple, si vous souhaitez que votre classe pour répondre à la fois à la `UITextFieldDelegate.textFieldShouldClear`: message et le `UIWebViewDelegate.webViewDidStartLoad`: dans la même instance d’une classe, vous devez utiliser la déclaration d’attribut [Exporter] :

```csharp
public class MyCallbacks : NSObject {
    [Export ("textFieldShouldClear:"]
    public bool should_we_clear (UITextField tf)
    {
        return true;
    }

    [Export ("webViewDidStartLoad:")]
    public void OnWebViewStart (UIWebView view)
    {
        Console.WriteLine ("Loading started");
    }
}
```

Le C# les noms de méthodes ne sont pas importantes ; tout ce qui est important sont les chaînes passées à l’attribut [Export].

Lorsque vous utilisez ce style de programmation, assurez-vous que les paramètres c# correspondent aux types réels qui passera par le moteur d’exécution.

<a name="Models" />

#### <a name="models"></a>Modèles

Dans les installations de stockage UIKit, ou répondeurs sont implémentées à l’aide des classes d’assistance, ceux-ci sont généralement appelés dans le code Objective-C délégués et ils sont implémentés en tant que protocoles.

Objective-C protocoles ressemblent à des interfaces, mais ils prennent en charge les méthodes facultatives : autrement dit, toutes les méthodes doivent être implémentées pour le protocole à utiliser.

Il existe deux façons de l’implémentation d’un modèle. Vous pouvez implémenter manuellement ou utiliser les définitions existantes fortement typées.


Le mécanisme manuels est nécessaire lorsque vous essayez d’implémenter une classe qui n’a pas été liée par Xamarin.iOS. Il est très facile à faire :

-  Indicateur de votre classe pour l’inscription auprès du runtime
-  Appliquez l’attribut de [Export] avec le nom de sélecteur réelle sur chaque méthode que vous souhaitez remplacer
-  Instancier la classe et le passer.

Par exemple, les éléments suivants implémentent uniquement une des méthodes facultatifs dans la définition de protocole UIApplicationDelegate :

```csharp
public class MyAppController : NSObject {
        [Export ("applicationDidFinishLaunching:")]
        public void FinishedLaunching (UIApplication app)
        {
                SetupWindow ();
        }
}
```

Le nom de sélecteur Objective-C (« applicationDidFinishLaunching : ») est déclaré avec l’attribut d’exportation et la classe est enregistrée avec le `[Register]` attribut.

Xamarin.iOS fournit fortement typées déclarations, prêts à l’emploi ne nécessitant pas de liaison manuelle. Pour prendre en charge ce modèle de programmation, le runtime Xamarin.iOS prend en charge l’attribut [modèle] sur une déclaration de classe. Cela informe le runtime qu’il doit associer pas toutes les méthodes dans la classe, à moins que les méthodes sont est implémenté de façon explicite.

Cela signifie que dans UIKit, les classes qui représentent un protocole avec les méthodes facultatives sont écrites comme suit :

```csharp
[Model]
public class SomeViewModel : NSObject {
    [Export ("someMethod:")]
    public virtual int SomeMethod (TheView view) {
       throw new ModelNotImplementedException ();
    }
    ...
}
```

Lorsque vous souhaitez implémenter un modèle qui implémente uniquement certaines des méthodes, il vous suffit consiste à substituer les méthodes que vous êtes intéressé et ignorez les autres méthodes. Le runtime se raccorde uniquement des méthodes remplacées, pas les méthodes d’origine au monde Objective-C.

L’équivalent à l’exemple précédent manuelle est :

```csharp
public class AppController : UIApplicationDelegate {
    public override void FinishedLaunching (UIApplication uia)
    {
     ...
    }
}
```

Les avantages sont qu’il n’est pas nécessaire d’examiner les fichiers d’en-tête Objective-C pour trouver le sélecteur, les types d’arguments, ou le mappage à c#, et que vous obtenez pas intellisense à partir de Visual Studio pour Mac, ainsi que des types forts


#### <a name="xib-outlets-and-c35"></a>C et XIB prises&#35;

> [!IMPORTANT]
> Cette section explique l’intégration de l’IDE avec prises lors de l’utilisation de fichiers XIB. Lorsque vous utilisez le Concepteur de Xamarin pour iOS, il est tout remplacé en entrant un nom sous **identité > nom** dans la section Propriétés de votre IDE, comme indiqué ci-dessous :
>
> [![](images/designeroutlet.png "Entrer un nom d’élément dans le concepteur iOS")](images/designeroutlet.png#lightbox)
>
>Pour plus d’informations sur le concepteur iOS, veuillez consulter la [présentation du concepteur iOS](~/ios/user-interface/designer/introduction.md#how-it-works) document.

Ceci est une description de bas niveau de comment intégrant des prises de courant avec c# et est fournie pour les utilisateurs expérimentés de Xamarin.iOS. Lorsque l’utilisation de Visual Studio pour Mac le mappage s’effectue automatiquement en arrière-plan à l’aide, code sur le vol est généré pour vous.

Lorsque vous concevez votre interface utilisateur avec Interface Builder, vous devrez créer uniquement l’aspect de l’application et établirez des connexions par défaut. Si vous souhaitez extraire des informations par programmation, modifier le comportement d’un contrôle lors de l’exécution ou modifier le contrôle lors de l’exécution, il est nécessaire de lier certains des contrôles à votre code managé.

Cette opération est effectuée en quelques étapes :

1.  Ajouter le **déclaration de sortie** à votre **le propriétaire du fichier**.
1.  Se connecter à votre contrôle à la **le propriétaire du fichier**.
1.  Store de l’interface utilisateur ainsi que les connexions dans votre fichier XIB/NIB.
1.  Charger le fichier NIB lors de l’exécution.
1.  Accéder à la variable de sortie.


Les étapes (1) à (3) sont traitées dans la documentation d’Apple pour la création d’interfaces avec Interface Builder.

Lorsque vous utilisez Xamarin.iOS, votre application devra créer une classe qui dérive de UIViewController. Il est implémenté, comme ceci :

```csharp
public class MyViewController : UIViewController {
    public MyViewController (string nibName, NSBundle bundle) : base (nibName, bundle)
    {
        // You can have as many arguments as you want, but you need to call
        // the base constructor with the provided nibName and bundle.
    }
}
```

Pour charger votre ViewController à partir d’un fichier NIB, vous effectuer cette opération :

```csharp
var controller = new MyViewController ("HelloWorld", NSBundle.MainBundle, this);
```

Cela charge l’interface utilisateur à partir de la NIB. Maintenant, pour accéder aux prises, il est nécessaire d’informer le runtime que nous souhaitons y accéder. Pour ce faire, le `UIViewController` sous-classe doit déclarer les propriétés et les annoter avec l’attribut [connexion]. Comme ceci :

```csharp
[Connect]
UITextField UserName {
    get {
        return (UITextField) GetNativeField ("UserName");
    }
    set {
        SetNativeField ("UserName", value);
    }
}
```

L’implémentation de la propriété est celui qui extrait et stocke la valeur pour le type natif réel.

Vous n’avez pas besoin en inquiéter lors de l’utilisation de Visual Studio pour Mac et InterfaceBuilder. Visual Studio pour Mac reflète automatiquement toutes les prises de déclaré avec le code dans une classe partielle qui est compilée en tant que partie de votre projet.

#### <a name="selectors"></a>Sélecteurs

Un concept fondamental de la programmation de Objective-C est sélecteurs. Vous serez souvent rencontrer des API qui vous obligent à transmettre un sélecteur ou attend votre code pour répondre à un sélecteur.

Création de nouveaux sélecteurs en c# est très facile : vous venez de créer une nouvelle instance de la `ObjCRuntime.Selector` classe et d’utiliser le résultat dans n’importe où dans l’API qui en a besoin. Exemple :

```csharp
var selector_add = new Selector ("add:plus:");
```

Pour un c# méthode en réponse à un appel de sélecteur, il doit hériter de la `NSObject` type et la méthode c# doivent être décorées avec le nom de sélecteur avec le `[Export]` attribut. Exemple :

```csharp
public class MyMath : NSObject {
    [Export ("add:plus:")]
    int Add (int first, int second)
    {
         return first + second;
    }
}
```

Notez ce sélecteur noms **doit** correspondre exactement, y compris tous les deux-points intermédiaires et de fin (« : »), le cas échéant.

#### <a name="nsobject-constructors"></a>Constructeurs NSObject

La plupart des classes dans Xamarin.iOS qui dérivent `NSObject` exposera des constructeurs spécifiques à la fonctionnalité de l’objet, mais ils seront également exposer des divers constructeurs qui ne sont pas immédiatement évidents.

Les constructeurs sont utilisés comme suit :

```csharp
public Foo (IntPtr handle)
```

Ce constructeur est utilisé pour instancier votre classe quand le runtime doit mapper votre classe à une classe non managée. Cela se produit lorsque vous chargez un fichier XIB/NIB.  À ce stade, le runtime Objective-C crée un objet dans le monde non géré, et ce constructeur sera appelé pour initialiser le côté géré.

En général, il vous suffit est d’appeler le constructeur de base avec le paramètre handle et dans le corps, effectuer une initialisation est nécessaire.

```csharp
public Foo ()
```

Il s’agit du constructeur par défaut pour une classe, et dans Xamarin.iOS fourni des classes, initialise la classe Foundation.NSObject et toutes les classes entre les deux et à la fin, cela lié à la Objective-C `init` méthode sur la classe.

```csharp
public Foo (NSObjectFlag x)
```

Ce constructeur est utilisé pour initialiser l’instance, mais empêcher l’appel de la méthode « init » Objective-C à la fin du code. Vous utilisez généralement cela lorsque vous avez déjà enregistré pour l’initialisation (lorsque vous utilisez `[Export]` sur votre constructeur) ou lorsque vous avez déjà fait votre initialisation via un autre moyen.

```csharp
public Foo (NSCoder coder)
```

Ce constructeur est fourni pour les cas où l’objet est initialisé à partir d’une instance NSCoding. Pour plus d’informations, consultez d’Apple [Archives et Guide de programmation de sérialisation.](https://developer.apple.com/mac/library/documentation/Cocoa/Conceptual/Archiving/index.html#//apple_ref/doc/uid/10000047i)

#### <a name="exceptions"></a>Exceptions

La conception de l’API Xamarin.iOS ne déclenche pas d’exceptions Objective-C en tant qu’exceptions c#. La conception impose qu’aucun garbage n’être envoyée en premier lieu au monde Objective-C et que toutes les exceptions qui doivent être produites sont produites par la liaison elle-même avant jamais des données non valides passées au monde Objective-C.

#### <a name="notifications"></a>Notifications

Dans iOS et OS X, les développeurs peuvent s’abonner aux notifications qui sont diffusées par la plateforme sous-jacente. Pour cela, à l’aide de la `NSNotificationCenter.DefaultCenter.AddObserver` (méthode). Le `AddObserver` méthode accepte deux paramètres ; une est la notification selon laquelle vous voulez vous abonner à l’autre est la méthode à appeler lorsque la notification est déclenchée.

Dans Xamarin.iOS et Xamarin.Mac, les clés pour les différentes notifications sont hébergés sur la classe qui déclenche les notifications. Par exemple, les notifications déclenchés par le `UIMenuController` sont hébergés en tant que `static NSString` propriétés dans le `UIMenuController` classes qui se terminent par le nom « Notification ».

### <a name="memory-management"></a>Gestion de la mémoire

Xamarin.iOS a un RÉCUPÉRATEUR de mémoire qui se chargera de libérer des ressources pour vous quand ils ne sont plus en cours d’utilisation. Outre le garbage collector, tous les objets qui dérivent de `NSObject` implémenter la `System.IDisposable` interface.

#### <a name="nsobject-and-idisposable"></a>NSObject et IDisposable

Exposer le `IDisposable` interface est un moyen pratique d’assister les développeurs dans la libération d’objets qui peuvent encapsuler des grands blocs de mémoire (par exemple, un `UIImage` peut se présenter comme simplement un pointeur inoffensif, mais peut pointer vers une image de 2 mégaoctets ) et d’autres ressources importantes et limitées (par exemple, une mémoire tampon de décodage vidéo).

NSObject implémente l’interface IDisposable et également le [modèle Dispose de .NET](https://msdn.microsoft.com/library/fs2xkftw.aspx). Cela permet aux développeurs de cette sous-classe NSObject pour remplacer le comportement de suppression et de libérer leurs ressources à la demande. Par exemple, considérez ce contrôleur d’affichage qui conserve un ensemble d’images :

```csharp
class MenuViewController : UIViewController {
    UIImage breakfast, lunch, dinner;
    [...]
    public override void Dispose (bool disposing)
    {
        if (disposing){
             if (breakfast != null) breakfast.Dispose (); breakfast = null;
             if (lunch != null) lunch.Dispose (); lunch = null;
             if (dinner != null) dinner.Dispose (); dinner = null;
        }
        base.Dispose (disposing)
    }
}
```

Lorsqu’un objet managé est supprimé, il n’est plus utile. Peut toujours avoir une référence aux objets, mais l’objet est non valide par tous les cas à ce stade. Certaines API .NET Vérifiez cela en levant un ObjectDisposedException si vous essayez d’accéder à toutes les méthodes sur un objet supprimé, par exemple :

```csharp
var image = UIImage.FromFile ("demo.png");
image.Dispose ();
image.XXX = false;  // this at this point is an invalid operation
```

Même si vous pouvez toujours accéder à la variable « image », il est vraiment une référence non valide et n’est plus pointe vers l’objet Objective-C qui détenait l’image.

Mais la suppression d’un objet en c# ne signifie pas que l’objet sera nécessairement être détruit. Il suffit de libérer la référence ayant c# à l’objet. Il est possible que l’environnement Cocoa peut conserver une référence autour de son propre usage. Par exemple, si vous définissez la propriété Image d’un UIImageView à une image, et puis de supprimer l’image, le UIImageView sous-jacent avait pris sa propre référence et conserve une référence à cet objet jusqu'à ce qu’elle est terminée à l’utiliser.

#### <a name="when-to-call-dispose"></a>Quand appeler la méthode Dispose

Vous devez appeler Dispose lorsque vous avez besoin de Mono dans se débarrasser de votre objet. Un cas d’usage possible est lorsque Mono n’a aucune connaissance que votre NSObject maintient en fait une référence à une ressource importante, telles que la mémoire ou un pool d’informations. Dans ce cas, vous devez appeler la méthode Dispose pour libérer immédiatement la référence à la mémoire, au lieu d’attendre de Mono effectuer un cycle de garbage collection.

Crée en interne, lorsque Mono [chaîne NSString fait référence à partir de chaînes C#](~/ios/internals/api-design/nsstring.md), il supprime les immédiatement pour réduire la quantité de travail que le garbage collector a à faire. Les objets moins environ à gérer, plus vite le garbage collector seront exécute.

#### <a name="when-to-keep-references-to-objects"></a>Lorsque conserver les références aux objets

Un effet secondaire qui a de la gestion automatique de la mémoire est que le garbage collector permet de supprimer les objets inutilisés tant qu’il n’existe aucune référence y. Cela parfois peut avoir des effets secondaires surprenants, par exemple, si vous créez une variable locale pour contenir votre contrôleur d’affichage de niveau supérieur, ou votre fenêtre de niveau supérieur et en faisant en sorte que ceux disparaissent derrière votre serveur.

Si vous ne conservez pas une référence dans votre statique ou des variables d’instance pour vos objets, Mono heureusement appelle la méthode Dispose() sur ces derniers, et ils vont sortir la référence à l’objet. Dans la mesure où cela est peut-être la référence uniquement en attente, le runtime Objective-C détruira l’objet pour vous.

## <a name="related-links"></a>Liens associés

- [Champs de liaison](~/cross-platform/macios/binding/objective-c-libraries.md#Binding_Fields)
