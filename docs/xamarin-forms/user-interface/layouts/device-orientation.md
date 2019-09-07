---
title: Orientation de l’appareil
description: Cet article explique comment les applications de Xamarin.Forms disposition l’aspect souhaité dans les orientations portrait et paysage.
ms.prod: xamarin
ms.assetid: 11A1D327-2DF3-4F3B-810D-6C95B71D27B2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/09/2015
ms.openlocfilehash: d988be9163c0a6e12735425a139c03d9641fa95c
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70772766"
---
# <a name="device-orientation"></a>Orientation de l’appareil

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-responsivelayout)

Il est important de prendre en compte la façon dont votre application sera utilisée et comment l’orientation paysage peut être incorporée pour améliorer l’expérience utilisateur. Dispositions individuelles peuvent être conçues pour prendre en charge plusieurs orientations et mieux utiliser l’espace disponible. Au niveau de l’application, rotation peut être désactivée ou activée.

<a name="Controlling_Orientation" />

## <a name="controlling-orientation"></a>Contrôler l’Orientation

Lorsque vous utilisez Xamarin.Forms, la méthode prise en charge du contrôle de l’orientation de l’appareil est d’utiliser les paramètres pour chaque projet individuel.

### <a name="ios"></a>iOS

Sur iOS, l’orientation de l’appareil est configuré pour les applications utilisant le **Info.plist** fichier. Ce fichier inclut les paramètres de l’orientation pour iPhone et iPod, ainsi que les paramètres pour iPad si l’application inclut en tant que cible. Vous trouverez ci-dessous des instructions spécifiques à votre IDE. Utilisez les options de l’IDE en haut de ce document pour sélectionner les instructions que vous aimeriez voir :

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Dans Visual Studio, ouvrez le projet iOS et ouvrez **Info.plist**. Le fichier s’ouvre dans un panneau de configuration, en commençant par l’onglet des informations de déploiement iPhone :

![iPhone, informations de déploiement dans Visual Studio](device-orientation-images/orientation-vs-iphone.png)

Pour configurer l’orientation iPad, sélectionnez le **iPad déploiement Info** onglet en haut à gauche du panneau, puis sélectionnez à partir des orientations disponibles :

![Orientations de l’appareil pris en charge dans Visual Studio](device-orientation-images/orientation-vs-ipad.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

Dans Visual Studio pour Mac, ouvrez le projet iOS et ouvrez **Info.plist**. Sous le **Application** onglet, sections seront disponibles pour définir l’orientation :

![iPhone, informations de déploiement dans Visual Studio pour Mac](device-orientation-images/orientation-xam-ui.png)

Si vous préférez modifier les valeurs à l’aide d’une interface de l’éditeur de clé-valeur, sélectionnez le **Source**> onglet en bas de l’écran :

![Prise en charge des Orientations de l’appareil dans Visual Studio pour Mac](device-orientation-images/orientation-xam-source.png)

-----

### <a name="android"></a>Android

Pour contrôler l’orientation sur Android, ouvrez **MainActivity.cs** et définissez l’orientation à l’aide de l’attribut décorant le `MainActivity` classe :

```csharp
namespace MyRotatingApp.Droid
{
    [Activity (Label = "MyRotatingApp.Droid", Icon = "@drawable/icon", Theme = "@style/MainTheme", MainLauncher = true, ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation, ScreenOrientation = ScreenOrientation.Landscape)] //This is what controls orientation
    public class MainActivity : FormsAppCompatActivity
    {
        protected override void OnCreate (Bundle bundle)
...
```

Xamarin.Android prend en charge plusieurs options permettant de spécifier l’orientation :

- **Paysage** &ndash; force l’orientation de l’application soit paysage, quelles que soient les données de capteur.
- **Portrait** &ndash; force l’orientation de l’application à être portrait, quelles que soient les données de capteur.
- **Utilisateur** &ndash; entraîne l’application qui sera présenté à l’aide de la préférence d’orientation de l’utilisateur.
- **Derrière** &ndash; provoque l’orientation de l’application à être identique à l’orientation de la [activité](xref:Android.App.Activity) derrière lui.
- **CAPTEUR** &ndash; entraîne l’orientation de l’application sera déterminé par le capteur, même si l’utilisateur a désactivé la rotation automatique.
- **SensorLandscape** &ndash; oblige l’application à utiliser l’orientation paysage lors de l’utilisation des données de capteur pour modifier la direction de l’écran est face (afin que l’écran n’est pas visible en tant que de haut en bas).
- **SensorPortrait** &ndash; oblige l’application à utiliser une orientation portrait lors de l’utilisation des données de capteur pour modifier la direction de l’écran est face (afin que l’écran n’est pas visible en tant que de haut en bas).
- **ReverseLandscape** &ndash; oblige l’application à utiliser l’orientation paysage, accessible sur la direction opposée de habituel, afin d’apparaître « envers. »
- **ReversePortrait** &ndash; oblige l’application à utiliser une orientation portrait, accessible sur la direction opposée de habituel, afin d’apparaître « envers. »
- **FullSensor** &ndash; entraîne l’application s’appuient sur des données de capteur pour sélectionner le bon sens (sur le 4 possible).
- **FullUser** &ndash; oblige l’application à utiliser les préférences d’orientation de l’utilisateur. Si la rotation automatique est activée, toutes les 4 orientations peuvent être utilisées.
- **UserLandscape** &ndash; _\[pas pris en charge\]_ entraîne l’application pour utiliser l’orientation paysage, sauf si l’utilisateur a rotation automatique activée, auquel cas il utilisera le capteur pour déterminer l’orientation. Cette option s’arrête la compilation.
- **UserPortrait** &ndash; _\[pas pris en charge\]_ entraîne l’application pour utiliser une orientation portrait, à moins que l’utilisateur a rotation automatique activée, auquel cas il utilisera le capteur pour déterminer l’orientation. Cette option s’arrête la compilation.
- **Verrouillé** &ndash; _\[pas pris en charge\]_ oblige l’application à utiliser l’orientation de l’écran, quel qu’il soit au lancement, sans répondre aux modifications de l’appareil de physique orientation. Cette option s’arrête la compilation.

Notez que les API natives Android fournissent un contrôle important sur la gestion de l’orientation, y compris les options qui contredisent explicitement l’utilisateur exprimée préférences.

### <a name="universal-windows-platform"></a>Plateforme Windows universelle

Sur la plateforme de Windows universelle (UWP), les orientations prises en charge sont définies le **Package.appxmanifest** fichier. Ouvrant le manifeste révèle un panneau de configuration où des orientations prises en charge peuvent être sélectionnées.

<a name="Reacting_to_Changes_in_Orientation" />

## <a name="reacting-to-changes-in-orientation"></a>Réagir aux modifications dans l’Orientation

Xamarin.Forms ne propose pas de tous les événements natifs pour avertir votre application de changements d’orientation dans le code partagé. Toutefois, le `SizeChanged` événements de la `Page` est déclenchée lorsque la largeur ou la hauteur de la `Page` modifications. Lors de la largeur de la `Page` est supérieure à la hauteur, l’appareil est en mode paysage. Pour plus d’informations, consultez [afficher une Image basée sur l’orientation de l’écran](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/Controls/screen-orientation).

> [!NOTE]
> Il existe un package NuGet existant et gratuit pour recevoir des notifications de changements d’orientation dans le code partagé. Consultez le [référentiel GitHub](https://github.com/aliozgur/Xamarin.Plugins/tree/master/DeviceOrientation) pour plus d’informations.

Il est également possible de remplacer le [ `OnSizeAllocated` ](xref:Xamarin.Forms.Page.OnSizeAllocated*) méthode sur un `Page`, insertion de toute disposition modifier logique. Le `OnSizeAllocated` méthode est appelée chaque fois qu’un `Page` est allouée à une nouvelle taille, ce qui se produit chaque fois que l’appareil est pivoté. Notez que l’implémentation de base de `OnSizeAllocated` effectue des fonctions de disposition importantes, il est donc important d’appeler l’implémentation de base dans la substitution :

```csharp
protected override void OnSizeAllocated(double width, double height)
{
    base.OnSizeAllocated(width, height); //must be called
}
```

Échec pour effectuer cette étape entraîne une page non fonctionnel.

Notez que le `OnSizeAllocated` méthode peut être appelée plusieurs fois lorsque vous faites pivoter un appareil. Modification de votre disposition chaque fois est inutile de ressources et peut entraîner le scintillement. Envisagez d’utiliser une variable d’instance au sein de votre page pour déterminer si l’orientation est en mode paysage ou portrait et redessiner uniquement lorsqu’il existe une modification :

```csharp
private double width = 0;
private double height = 0;

protected override void OnSizeAllocated(double width, double height)
{
    base.OnSizeAllocated(width, height); //must be called
    if (this.width != width || this.height != height)
    {
        this.width = width;
        this.height = height;
        //reconfigure layout
    }
}
```

Une fois qu’une modification de l’orientation de l’appareil a été détectée, vous souhaiterez ajouter ou supprimer des affichages supplémentaires à votre interface utilisateur de réagir aux changements de l’espace disponible. Par exemple, considérez la calculatrice intégrée sur chaque plateforme en portrait :

![](device-orientation-images/calculator-portrait.png "Application de calculatrice en Portrait")

et paysage :

![](device-orientation-images/calculator-landscape.png "Application de calculatrice en mode paysage")

Notez que les applications tirer parti de l’espace disponible en ajoutant davantage de fonctionnalités en mode paysage.

<a name="Responsive_Layout" />

## <a name="responsive-layout"></a>Disposition dynamique

Il est possible d’interfaces de conception à l’aide de mises en page intégrés afin qu’ils passent normalement lors de la rotation de l’appareil. Lorsque vous concevez des interfaces qui continueront à être attrayante lors de la réponse aux modifications de l’orientation envisager les règles générales suivantes :

- **Faites attention aux rapports de** &ndash; modifications dans l’orientation peuvent entraîner des problèmes lors de certaines hypothèses en ce qui concerne les rapports. Par exemple, une vue ayant suffisamment d’espace disque à 1/3 de l’espace vertical d’un écran en mode portrait ne correspondre pas 1/3 de l’espace vertical en mode paysage.
- **Soyez prudent avec les valeurs absolues** &ndash; absolue (en pixels) valeurs pertinentes en portrait est peut-être pas judicieux en mode paysage. Lorsque les valeurs absolues sont nécessaires, utilisez dispositions imbriquées pour isoler leur impact. Par exemple, il serait judicieux d’utiliser les valeurs absolues dans un `TableView` `ItemTemplate` lorsque le modèle d’élément a une hauteur uniforme de garantie.

Les règles ci-dessus s’appliquent également lorsque l’implémentation des interfaces pour plusieurs tailles d’écran et sont généralement considérés comme des meilleures pratiques. Le reste de ce guide explique des exemples spécifiques de dispositions réactives à l’aide de chacune des mises en page principales dans Xamarin.Forms.

> [!NOTE]
> Pour plus de clarté, les sections suivantes montrent comment implémenter des dispositions réactives à l’aide qu’un seul type de `Layout` à la fois. Dans la pratique, il est souvent plus simple de combiner `Layout`s pour obtenir une présentation souhaitée à l’aide de la plus simple ou plus intuitive `Layout` pour chaque composant.

### <a name="stacklayout"></a>StackLayout

Tenez compte de l’application suivante, affichée en portrait :

![](device-orientation-images/photo-stack-portrait.png "Application de photos en Portrait")

et paysage :

![](device-orientation-images/photo-stack-landscape.png "Application de photos de paysage")

Ceci se produit avec le XAML suivant :

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="ResponsiveLayout.StackLayoutPageXaml"
Title="Stack Photo Editor - XAML">
    <ContentPage.Content>
        <StackLayout Spacing="10" Padding="5" Orientation="Vertical"
        x:Name="outerStack"> <!-- can change orientation to make responsive -->
            <ScrollView>
                <StackLayout Spacing="5" HorizontalOptions="FillAndExpand"
                    WidthRequest="1000">
                    <StackLayout Orientation="Horizontal">
                        <Label Text="Name: " WidthRequest="75"
                            HorizontalOptions="Start" />
                        <Entry Text="deer.jpg"
                            HorizontalOptions="FillAndExpand" />
                    </StackLayout>
                    <StackLayout Orientation="Horizontal">
                        <Label Text="Date: " WidthRequest="75"
                            HorizontalOptions="Start" />
                        <Entry Text="07/05/2015"
                            HorizontalOptions="FillAndExpand" />
                    </StackLayout>
                    <StackLayout Orientation="Horizontal">
                        <Label Text="Tags:" WidthRequest="75"
                            HorizontalOptions="Start" />
                        <Entry Text="deer, tiger"
                            HorizontalOptions="FillAndExpand" />
                    </StackLayout>
                    <StackLayout Orientation="Horizontal">
                        <Button Text="Save" HorizontalOptions="FillAndExpand" />
                    </StackLayout>
                </StackLayout>
            </ScrollView>
            <Image  Source="deer.jpg" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

Certains C# est utilisé pour modifier l’orientation de `outerStack` selon l’orientation de l’appareil :

```csharp
protected override void OnSizeAllocated (double width, double height){
    base.OnSizeAllocated (width, height);
    if (width != this.width || height != this.height) {
        this.width = width;
        this.height = height;
        if (width > height) {
            outerStack.Orientation = StackOrientation.Horizontal;
        } else {
            outerStack.Orientation = StackOrientation.Vertical;
        }
    }
}
```

Notez les points suivants :

- `outerStack` est ajustée pour présenter l’image et les contrôles comme une pile horizontale ou verticale en fonction de l’orientation, pour tirer le meilleur parti de l’espace disponible.

### <a name="absolutelayout"></a>DispositionAbsolue

Tenez compte de l’application suivante, affichée en portrait :

![](device-orientation-images/photo-abs-portrait.png "Application de photos en Portrait")

et paysage :

![](device-orientation-images/photo-abs-landscape.png "Application de photos de paysage")

Ceci se produit avec le XAML suivant :

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="ResponsiveLayout.AbsoluteLayoutPageXaml"
Title="AbsoluteLayout - XAML" BackgroundImageSource="deer.jpg">
    <ContentPage.Content>
        <AbsoluteLayout>
            <ScrollView AbsoluteLayout.LayoutBounds="0,0,1,1"
                AbsoluteLayout.LayoutFlags="PositionProportional,SizeProportional">
                <AbsoluteLayout>
                    <Image Source="deer.jpg"
                        AbsoluteLayout.LayoutBounds=".5,0,300,300"
                        AbsoluteLayout.LayoutFlags="PositionProportional" />
                    <BoxView Color="#CC1A7019" AbsoluteLayout.LayoutBounds=".5
                        300,.7,50" AbsoluteLayout.LayoutFlags="XProportional
                        WidthProportional" />
                    <Label Text="deer.jpg" AbsoluteLayout.LayoutBounds = ".5
                        310,1, 50" AbsoluteLayout.LayoutFlags="XProportional
                        WidthProportional" HorizontalTextAlignment="Center" TextColor="White" />
                </AbsoluteLayout>
            </ScrollView>
            <Button Text="Previous" AbsoluteLayout.LayoutBounds="0,1,.5,60"
                AbsoluteLayout.LayoutFlags="PositionProportional
                    WidthProportional"
                BackgroundColor="White" TextColor="Green" BorderRadius="0" />
            <Button Text="Next" AbsoluteLayout.LayoutBounds="1,1,.5,60"
                AbsoluteLayout.LayoutFlags="PositionProportional
                    WidthProportional" BackgroundColor="White"
                    TextColor="Green" BorderRadius="0" />
        </AbsoluteLayout>
    </ContentPage.Content>
</ContentPage>
```

Notez les points suivants :

- En raison de la façon dont la page a été disposée, il est inutile pour le code de procédure pour introduire la réactivité.
- Le `ScrollView` est utilisé pour permettre à l’étiquette soit visible, même lorsque la hauteur de l’écran est inférieure à la somme des hauteurs des boutons et l’image fixes.

### <a name="relativelayout"></a>RelativeLayout

Tenez compte de l’application suivante, affichée en portrait :

![](device-orientation-images/photo-rel-portrait.png "Application de photos en Portrait")

et paysage :

![](device-orientation-images/photo-rel-landscape.png "Application de photos de paysage")

Ceci se produit avec le XAML suivant :

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="ResponsiveLayout.RelativeLayoutPageXaml"
Title="RelativeLayout - XAML"
BackgroundImageSource="deer.jpg">
    <ContentPage.Content>
        <RelativeLayout x:Name="outerLayout">
            <BoxView BackgroundColor="#AA1A7019"
                RelativeLayout.WidthConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=1}"
                RelativeLayout.HeightConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Height,Factor=1}"
                RelativeLayout.XConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=0,Constant=0}"
                RelativeLayout.YConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Height,Factor=0,Constant=0}" />
            <ScrollView
                RelativeLayout.WidthConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=1}"
                RelativeLayout.HeightConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Height,Factor=1,Constant=-60}"
                RelativeLayout.XConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=0,Constant=0}"
                RelativeLayout.YConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Height,Factor=0,Constant=0}">
                <RelativeLayout>
                    <Image Source="deer.jpg" x:Name="imageDeer"
                        RelativeLayout.WidthConstraint="{ConstraintExpression
                            Type=RelativeToParent,Property=Width,Factor=.8}"
                        RelativeLayout.XConstraint="{ConstraintExpression
                            Type=RelativeToParent,Property=Width,Factor=.1}"
                        RelativeLayout.YConstraint="{ConstraintExpression
                            Type=RelativeToParent,Property=Height,Factor=0,Constant=10}" />
                    <Label Text="deer.jpg" HorizontalTextAlignment="Center"
                        RelativeLayout.WidthConstraint="{ConstraintExpression
                            Type=RelativeToParent,Property=Width,Factor=1}"
                        RelativeLayout.HeightConstraint="{ConstraintExpression
                            Type=RelativeToParent,Property=Height,Factor=0,Constant=75}"
                        RelativeLayout.XConstraint="{ConstraintExpression
                            Type=RelativeToParent,Property=Width,Factor=0,Constant=0}"
                        RelativeLayout.YConstraint="{ConstraintExpression
                            Type=RelativeToView,ElementName=imageDeer,Property=Height,Factor=1,Constant=20}" />
                </RelativeLayout>

            </ScrollView>

            <Button Text="Previous" BackgroundColor="White" TextColor="Green" BorderRadius="0"
                RelativeLayout.YConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Height,Factor=1,Constant=-60}"
                RelativeLayout.XConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=0,Constant=0}"
                RelativeLayout.HeightConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=0,Constant=60}"
                RelativeLayout.WidthConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=.5}"
                 />
            <Button Text="Next" BackgroundColor="White" TextColor="Green" BorderRadius="0"
                RelativeLayout.XConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=.5}"
                RelativeLayout.YConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Height,Factor=1,Constant=-60}"
                RelativeLayout.HeightConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=0,Constant=60}"
                RelativeLayout.WidthConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=.5}"
                />
        </RelativeLayout>
    </ContentPage.Content>
</ContentPage>

```

Notez les points suivants :

- En raison de la façon dont la page a été disposée, il est inutile pour le code de procédure pour introduire la réactivité.
- Le `ScrollView` est utilisé pour permettre à l’étiquette soit visible, même lorsque la hauteur de l’écran est inférieure à la somme des hauteurs des boutons et l’image fixes.

### <a name="grid"></a>Grille

Tenez compte de l’application suivante, affichée en portrait :

![](device-orientation-images/photo-grid-portrait.png "Application de photos en Portrait")

et paysage :

![](device-orientation-images/photo-grid-landscape.png "Application de photos de paysage")

Ceci se produit avec le XAML suivant :

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="ResponsiveLayout.GridPageXaml"
Title="Grid - XAML">
    <ContentPage.Content>
        <Grid x:Name="outerGrid">
            <Grid.RowDefinitions>
                <RowDefinition Height="*" />
                <RowDefinition Height="60" />
            </Grid.RowDefinitions>
            <Grid x:Name="innerGrid" Grid.Row="0" Padding="10">
                <Grid.RowDefinitions>
                    <RowDefinition Height="*" />
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="*" />
                    <ColumnDefinition Width="*" />
                </Grid.ColumnDefinitions>
                <Image Source="deer.jpg" Grid.Row="0" Grid.Column="0" HeightRequest="300" WidthRequest="300" />
                <Grid x:Name="controlsGrid" Grid.Row="0" Grid.Column="1" >
                    <Grid.RowDefinitions>
                        <RowDefinition Height="Auto" />
                        <RowDefinition Height="Auto" />
                        <RowDefinition Height="Auto" />
                    </Grid.RowDefinitions>
                    <Grid.ColumnDefinitions>
                        <ColumnDefinition Width="Auto" />
                        <ColumnDefinition Width="*" />
                    </Grid.ColumnDefinitions>
                    <Label Text="Name:" Grid.Row="0" Grid.Column="0" />
                    <Label Text="Date:" Grid.Row="1" Grid.Column="0" />
                    <Label Text="Tags:" Grid.Row="2" Grid.Column="0" />
                    <Entry Grid.Row="0" Grid.Column="1" />
                    <Entry Grid.Row="1" Grid.Column="1" />
                    <Entry Grid.Row="2" Grid.Column="1" />
                </Grid>
            </Grid>
            <Grid x:Name="buttonsGrid" Grid.Row="1">
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="*" />
                    <ColumnDefinition Width="*" />
                    <ColumnDefinition Width="*" />
                </Grid.ColumnDefinitions>
                <Button Text="Previous" Grid.Column="0" />
                <Button Text="Save" Grid.Column="1" />
                <Button Text="Next" Grid.Column="2" />
            </Grid>
        </Grid>
    </ContentPage.Content>
</ContentPage>
```

En même temps que le code procédural suivant pour gérer les changements de rotation :

```csharp
private double width;
private double height;

protected override void OnSizeAllocated (double width, double height){
    base.OnSizeAllocated (width, height);
    if (width != this.width || height != this.height) {
        this.width = width;
        this.height = height;
        if (width > height) {
            innerGrid.RowDefinitions.Clear();
            innerGrid.ColumnDefinitions.Clear ();
            innerGrid.RowDefinitions.Add (new RowDefinition{ Height = new GridLength (1, GridUnitType.Star) });
            innerGrid.ColumnDefinitions.Add (new ColumnDefinition { Width = new GridLength (1, GridUnitType.Star) });
            innerGrid.ColumnDefinitions.Add (new ColumnDefinition { Width = new GridLength (1, GridUnitType.Star) });
            innerGrid.Children.Remove (controlsGrid);
            innerGrid.Children.Add (controlsGrid, 1, 0);
        } else {
            innerGrid.RowDefinitions.Clear();
            innerGrid.ColumnDefinitions.Clear ();
            innerGrid.ColumnDefinitions.Add (new ColumnDefinition{ Width = new GridLength (1, GridUnitType.Star) });
            innerGrid.RowDefinitions.Add (new RowDefinition { Height = new GridLength (1, GridUnitType.Auto) });
            innerGrid.RowDefinitions.Add (new RowDefinition { Height = new GridLength (1, GridUnitType.Star) });
            innerGrid.Children.Remove (controlsGrid);
            innerGrid.Children.Add (controlsGrid, 0, 1);
        }
    }
}
```

Notez les points suivants :

- En raison de la façon dont la page a été disposée, il existe une méthode pour modifier la position de grille des contrôles.

## <a name="related-links"></a>Liens associés

- [Disposition (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)
- [Exemple BusinessTumble (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-businesstumble)
- [Disposition dynamique (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-responsivelayout)
- [Afficher une Image basée sur l’orientation de l’écran](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/Controls/screen-orientation)
