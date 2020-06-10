---
titre : « Xamarin.Forms RelativeLayout » Description : « cet article explique comment utiliser la Xamarin.Forms classe RelativeLayout pour créer des interfaces utilisateur qui s’adaptent à n’importe quelle taille d’écran. »
ms. Prod : xamarin ms. AssetID : 2530BCB8-01B8-4C4F-BF14-CA53659F1B5A ms. Technology : xamarin-Forms Author : davidbritch ms. Author : dabritch ms. Date : 11/25/2015 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-relativelayout"></a>Xamarin.FormsRelativeLayout

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)

`RelativeLayout`est utilisé pour positionner et dimensionner les vues par rapport aux propriétés des affichages de mise en page ou frères. Contrairement `AbsoluteLayout` `RelativeLayout` à, n’a pas le concept de l’ancre en déplacement et n’a pas de fonctions pour positionner les éléments par rapport aux bords inférieur ou droit de la disposition. `RelativeLayout`prend en charge le positionnement des éléments en dehors de ses propres limites.

[![](relative-layout-images/layouts-sml.png "Xamarin.Forms Layouts")](relative-layout-images/layouts.png#lightbox "Xamarin.Forms Layouts")

## <a name="purpose"></a>Objectif

`RelativeLayout`peut être utilisé pour positionner des vues sur l’écran par rapport à la disposition générale ou à d’autres vues.

![](relative-layout-images/flag.png "RelativeLayout Exploration")

## <a name="usage"></a>Usage

### <a name="understanding-constraints"></a>Fonctionnement des contraintes

Le positionnement et le dimensionnement d’une vue dans un `RelativeLayout` s’effectuent avec des contraintes. Une expression de contrainte peut inclure les informations suivantes :

- **Type** &ndash; indique si la contrainte est relative au parent ou à une autre vue.
- **Propriété** &ndash; propriété à utiliser comme base pour la contrainte.
- **Facteur** &ndash; facteur à appliquer à la valeur de propriété.
- **Constante** &ndash; valeur à utiliser comme décalage de la valeur.
- **ElementName** &ndash; nom de la vue à laquelle la contrainte est relative.

En XAML, les contraintes sont exprimées en tant que `ConstraintExpression` s. Prenons l’exemple suivant :

```xaml
<BoxView Color="Green" WidthRequest="50" HeightRequest="50"
    RelativeLayout.XConstraint =
      "{ConstraintExpression Type=RelativeToParent,
                             Property=Width,
                             Factor=0.5,
                             Constant=-100}"
    RelativeLayout.YConstraint =
      "{ConstraintExpression Type=RelativeToParent,
                             Property=Height,
                             Factor=0.5,
                             Constant=-100}" />
```

En C#, les contraintes sont exprimées un peu différemment, à l’aide de fonctions plutôt que d’expressions sur la vue. Les contraintes sont spécifiées en tant qu’arguments de la méthode de la disposition `Add` :

```csharp
layout.Children.Add(box, Constraint.RelativeToParent((parent) =>
    {
      return (.5 * parent.Width) - 100;
    }),
    Constraint.RelativeToParent((parent) =>
    {
        return (.5 * parent.Height) - 100;
    }),
    Constraint.Constant(50), Constraint.Constant(50));
```

Notez les aspects suivants de la mise en page ci-dessus :

- Les `x` `y` contraintes et sont spécifiées avec leurs propres contraintes.
- En C#, les contraintes relatives sont définies en tant que fonctions. Les concepts tels que `Factor` ne sont pas présents, mais peuvent être implémentés manuellement.
- La `x` coordonnée de la zone est définie sur la moitié de la largeur du parent,-100.
- La `y` coordonnée de la zone est définie sur la moitié de la hauteur du parent,-100.

> [!NOTE]
> En raison de la façon dont les contraintes sont définies, il est possible de créer des dispositions plus complexes en C# que celles qui peuvent être spécifiées avec XAML.

Les deux exemples ci-dessus définissent des contraintes `RelativeToParent` &ndash; , car leurs valeurs sont relatives à l’élément parent. Il est également possible de définir des contraintes par rapport à une autre vue. Cela permet une mise en page plus intuitive (pour les développeurs) et peut rendre l’objectif de votre code de disposition plus évident.

Considérez une disposition dans laquelle un élément doit avoir une valeur de 20 pixels inférieure à une autre. Si les deux éléments sont définis avec des valeurs constantes, la contrainte de la version inférieure peut être `Y` définie comme une constante de 20 pixels supérieure à la `Y` contrainte de l’élément supérieur. Cette approche est très brève si l’élément supérieur est positionné à l’aide d’une proportion, de sorte que la taille en pixels n’est pas connue. Dans ce cas, la limitation de l’élément en fonction de la position d’un autre élément est plus robuste :

```xaml
<RelativeLayout>
    <BoxView Color="Red" x:Name="redBox"
        RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent,
            Property=Height,Factor=.15,Constant=0}"
        RelativeLayout.WidthConstraint="{ConstraintExpression
            Type=RelativeToParent,Property=Width,Factor=1,Constant=0}"
        RelativeLayout.HeightConstraint="{ConstraintExpression
            Type=RelativeToParent,Property=Height,Factor=.8,Constant=0}" />
    <BoxView Color="Blue"
        RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToView,
            ElementName=redBox,Property=Y,Factor=1,Constant=20}"
        RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToView,
            ElementName=redBox,Property=X,Factor=1,Constant=20}"
        RelativeLayout.WidthConstraint="{ConstraintExpression
            Type=RelativeToParent,Property=Width,Factor=.5,Constant=0}"
        RelativeLayout.HeightConstraint="{ConstraintExpression
            Type=RelativeToParent,Property=Height,Factor=.5,Constant=0}" />
</RelativeLayout>
```

Pour accomplir la même disposition en C# :

```csharp
layout.Children.Add (redBox, Constraint.RelativeToParent ((parent) => {
        return parent.X;
    }), Constraint.RelativeToParent ((parent) => {
        return parent.Y * .15;
    }), Constraint.RelativeToParent((parent) => {
        return parent.Width;
    }), Constraint.RelativeToParent((parent) => {
        return parent.Height * .8;
    }));
layout.Children.Add (blueBox, Constraint.RelativeToView (redBox, (Parent, sibling) => {
        return sibling.X + 20;
    }), Constraint.RelativeToView (blueBox, (parent, sibling) => {
        return sibling.Y + 20;
    }), Constraint.RelativeToParent((parent) => {
        return parent.Width * .5;
    }), Constraint.RelativeToParent((parent) => {
        return parent.Height * .5;
    }));
```

Cela produit la sortie suivante, avec la position de la zone bleue déterminée _par rapport_ à la position de la boîte rouge :

![](relative-layout-images/red-blue-box.png "RelativeLayout with Red and Blue BoxViews")

### <a name="sizing"></a>Dimensionnement

Les vues présentées par `RelativeLayout` ont deux options pour spécifier leur taille :

- `HeightRequest & WidthRequest`
- `RelativeLayout.WidthConstraint` & `RelativeLayout.HeightConstraint`

`HeightRequest`et `WidthRequest` spécifient la hauteur et la largeur prévues de la vue, mais elles peuvent être remplacées par des dispositions si nécessaire. `WidthConstraint`et `HeightConstraint` prennent en charge la définition de la hauteur et de la largeur en tant que valeur relative aux propriétés de la disposition ou d’une autre vue, ou en tant que valeur constante.

## <a name="exploring-a-complex-layout"></a>Exploration d’une disposition complexe
Chacune des dispositions présente des forces et des faiblesses pour la création de dispositions particulières. Tout au long de cette série d’Articles de la disposition, un exemple d’application a été créé avec la même mise en page implémentée à l’aide de trois dispositions différentes.

Examinez le code XAML suivant :

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="TheBusinessTumble.RelativeLayoutPage"
BackgroundColor="Maroon"
Title="RelativeLayout">
    <ContentPage.Content>
    <ScrollView>
      <RelativeLayout>
        <BoxView Color="Gray" HeightRequest="100"
            RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=1}" />
        <Button BorderRadius="35" x:Name="imageCircleBack"
            BackgroundColor="Maroon" HeightRequest="70" WidthRequest="70" RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent,Property=Width, Factor=.5, Constant = -35}" RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Factor=0, Property=Y, Constant=70}" />
        <Button BorderRadius="30" BackgroundColor="Red" HeightRequest="60"
            WidthRequest="60" RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToView, ElementName=imageCircleBack, Property=X, Factor=1,Constant=5}" RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Factor=0, Property=Y, Constant=75}" />
        <Label Text="User Name" FontAttributes="Bold" FontSize="26"
            HorizontalTextAlignment="Center" RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Y, Factor=0, Constant=140}" RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=1}" />
        <Entry Text="Bio + Hashtags" TextColor="White" BackgroundColor="Maroon"
            RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Y, Factor=0, Constant=180}" RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=1}" />
        <RelativeLayout BackgroundColor="White" RelativeLayout.YConstraint="
            {ConstraintExpression Type=RelativeToParent, Property=Y, Factor=0, Constant=220}" HeightRequest="60" RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=1}" >
            <BoxView BackgroundColor="Black" WidthRequest="50"
                HeightRequest="50" RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Y, Factor=0, Constant=5}" RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=X, Factor=0, Constant=5}" />
            <BoxView BackgroundColor="Maroon" WidthRequest="50"
                HeightRequest="50" RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Y, Factor=0, Constant=5}" RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=0.5}" />
            <Label FontSize="14" TextColor="Black" Text="Accent Color"
                RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Y, Factor=0, Constant=20}" RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=X, Factor=0, Constant=60}" />
            <Label FontSize="14" TextColor="Black" Text="Primary Color"
                RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Y, Factor=0, Constant=20}" RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=0.5, Constant=55}" />
        </RelativeLayout>
        <RelativeLayout Padding="5,0,0,0">
          <Label FontSize="14" Text="Age:" TextColor="White"
            RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0,Constant=305}"
            RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=0, Constant=10}"
            RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width,Factor=.25,Constant=0}"
            RelativeLayout.HeightConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0,Constant=50}" />
          <Entry Text="35" TextColor="White" BackgroundColor="Maroon"
            RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0,Constant=280}"
            RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=0.3, Constant=0}"
            RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width,Factor=0.75,Constant=0}"
            RelativeLayout.HeightConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0,Constant=50}" />
        </RelativeLayout>
        <RelativeLayout  Padding="5,0,0,0">
          <Label FontSize="14" Text="Interests:" TextColor="White"
            RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0,Constant=345}"
            RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=0, Constant=10}"
            RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width,Factor=.25,Constant=0}"
            RelativeLayout.HeightConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0,Constant=50}" />
          <Entry Text="Xamarin.Forms" TextColor="White" BackgroundColor="Maroon"
            RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0,Constant=320}"
            RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=0.3, Constant=0}"
            RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width,Factor=0.75,Constant=0}"
            RelativeLayout.HeightConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0,Constant=50}" />
        </RelativeLayout>
        <RelativeLayout  Padding="5,0,0,0">
          <Label FontSize="14" Text="Ask me about:" TextColor="White"
            LineBreakMode="WordWrap"
            RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0,Constant=395}"
            RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=0, Constant=10}"
            RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width,Factor=.25,Constant=0}"
            RelativeLayout.HeightConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0,Constant=50}" />
          <Entry Text="Xamarin, C#, .NET, Mono" TextColor="White"
            BackgroundColor="Maroon"
            RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0,Constant=370}"
            RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=0.3, Constant=0}"
            RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width,Factor=0.75,Constant=0}"
            RelativeLayout.HeightConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0,Constant=50}" />
        </RelativeLayout>
      </RelativeLayout>
    </ScrollView>
  </ContentPage.Content>
</ContentPage>
```

Le code ci-dessus donne la disposition suivante :

![](relative-layout-images/relative.png "Complex RelativeLayout")

Notez que `RelativeLayouts` les s sont imbriquées, car dans certains cas, l’imbrication des dispositions peut être plus facile que la présentation de tous les éléments dans la même disposition. Notez également que certains éléments sont `RelativeToView` , car cela permet une mise en page plus simple et plus intuitive lorsque les relations entre les vues sont placées dans le positionnement du repère.

## <a name="related-links"></a>Liens connexes

- [Disposition (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)
- [Exemple BusinessTumble (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-businesstumble)
