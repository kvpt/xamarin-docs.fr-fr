---
titre : «pourquoi mon Xamarin.Forms . Le projet Android Maps échoue avec COMPILETODALVIK erreur de niveau supérieur inattendue ?»
ms. topic : Troubleshooting ms. Prod : xamarin ms. AssetID : C0251EB1-F509-47AD-98D6-846AF46425E5 ms. Technology : xamarin-Forms Author : davidbritch ms. Author : dabritch ms. Date : 04/25/2017 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="why-does-my-xamarinformsmaps-android-project-fail-with-compiletodalvik-unexpected-top-level-error"></a>Pourquoi My Xamarin.Forms . Le projet Android Maps échoue avec COMPILETODALVIK erreur de niveau supérieur inattendue ?

Cette erreur peut se produire dans le bloc d’erreurs de Visual Studio pour Mac ou dans la fenêtre sortie de la génération de Visual Studio. dans les projets Android utilisant Xamarin.Forms . Mount.

Cela est généralement résolu en accroissant la taille du tas Java pour votre projet Xamarin. Android. Pour augmenter la taille du tas, procédez comme suit :

## <a name="visual-studio"></a>Visual Studio

1. Cliquez avec le bouton droit sur le projet Android & ouvrir les options du projet.
2. Accéder aux **options Android-> avancé**
3. Dans la zone de texte taille du tas Java, entrez 1G.
4. Regénérez le projet.

![Capture d’écran des options de projet Visual Studio](maps-compiletodalvik-error-images/vsjavaheap.png "Options de génération Android dans Visual Studio")

## <a name="visual-studio-for-mac"></a>Visual Studio pour Mac

1. Cliquez avec le bouton droit sur le projet Android & ouvrir les options du projet.
2. Accédez à **Build-> Android Build-> Advanced**
3. Dans la zone de texte taille du tas Java, entrez 1G.
4. Regénérez le projet.  

![Capture d’écran des options du projet Visual Studio pour Mac](maps-compiletodalvik-error-images/xsjavaheap.png "Options de génération Android dans Visual Studio pour Mac")
