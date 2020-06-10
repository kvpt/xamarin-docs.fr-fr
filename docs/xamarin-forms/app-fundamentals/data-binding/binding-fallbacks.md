---
titre : « Xamarin.Forms liaison de secours » Description : « cet article explique comment rendre les liaisons plus robustes en définissant les valeurs de secours qui seront utilisées en cas d’échec de la liaison. »
ms. Prod : xamarin ms. AssetID : 637ACD9D-3E5D-4014-86DE-A77D1FEF238A ms. Technology : xamarin-Forms Author : davidbritch ms. Author : dabritch ms. Date : 08/16/2018 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-binding-fallbacks"></a>Xamarin.FormsLiaison de secours

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)

Une liaison de données échoue parfois car la source de la liaison ne peut pas être résolue ou parce que la liaison réussit mais retourne une valeur `null`. De tels scénarios peuvent être gérés avec des convertisseurs de valeurs ou du code supplémentaire, mais il est possible de renforcer les liaisons de données en définissant des valeurs de repli à utiliser si le processus de liaison échoue. Pour ce faire, vous devez définir [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue) les [`TargetNullValue`](xref:Xamarin.Forms.BindingBase.TargetNullValue) Propriétés et dans une expression de liaison. Étant donné que ces propriétés résident dans la [`BindingBase`](xref:Xamarin.Forms.BindingBase) classe, elles peuvent être utilisées avec des liaisons, des liaisons compilées et l' `Binding` extension de balisage.

> [!NOTE]
> L’utilisation des [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue) [`TargetNullValue`](xref:Xamarin.Forms.BindingBase.TargetNullValue) Propriétés et dans une expression de liaison est facultative.

## <a name="defining-a-fallback-value"></a>Définition d’une valeur de repli

La [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue) propriété permet de définir une valeur de secours qui sera utilisée lorsque la *source* de liaison ne peut pas être résolue. Un scénario courant pour définir cette propriété est lors d’une liaison à des propriétés de source qui n’existent peut-être pas sur tous les objets d’une collection liée de types hétérogènes.

La page **MonkeyDetail** illustre la définition de la propriété [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue) :

```xaml
<Label Text="{Binding Population, FallbackValue='Population size unknown'}"
       ... />   
```

La liaison sur le [`Label`](xref:Xamarin.Forms.Label) définit une [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue) valeur qui sera définie sur la cible si la source de liaison ne peut pas être résolue. Par conséquent, la valeur définie par la propriété `FallbackValue` s’affiche si la propriété `Population` n’existe pas sur l’objet lié. Notez qu’ici la valeur de propriété `FallbackValue` est délimitée par des guillemets simples (apostrophes).

Plutôt que de définir [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue) des valeurs de propriété en ligne, il est recommandé de les définir en tant que ressources dans un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) . L’avantage de cette approche est que ces valeurs sont définies une seule fois dans un emplacement unique et sont plus facilement localisables. Les ressources peuvent ensuite être récupérées à l’aide de l’extension de balisage `StaticResource` :

```xaml
<Label Text="{Binding Population, FallbackValue={StaticResource populationUnknown}}"
       ... />  
```

> [!NOTE]
> Il n’est pas possible de définir la propriété `FallbackValue` avec une expression de liaison.

Voici le programme en cours d’exécution :

![Liaison FallbackValue](binding-fallbacks-images/bindingunavailable-detail-cropped.png "Liaison FallbackValue")

Lorsque la `FallbackValue` propriété n’est pas définie dans une expression de liaison et que le chemin de liaison ou une partie du chemin d’accès n’est pas résolu, [`BindableProperty.DefaultValue`](xref:Xamarin.Forms.BindableProperty.DefaultValue) est défini sur la cible. Toutefois, lorsque la propriété `FallbackValue` est définie et que le chemin de liaison ou une partie de ce chemin n’est pas résolu(e), la valeur de la propriété `FallbackValue` est définie sur la cible. Par conséquent, dans la page **MonkeyDetail**, le [`Label`](xref:Xamarin.Forms.Label) affiche « Population size unknown » (Taille de la population inconnue), car l’objet lié n’a pas de propriété `Population`.

> [!IMPORTANT]
> Un convertisseur de valeurs défini n’est pas exécuté dans une expression de liaison lorsque la [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue) propriété est définie.

## <a name="defining-a-null-replacement-value"></a>Définition d’une valeur de remplacement de null

La [`TargetNullValue`](xref:Xamarin.Forms.BindingBase.TargetNullValue) propriété permet de définir une valeur de remplacement qui sera utilisée lorsque la *source* de liaison sera résolue, mais la valeur est `null` . Un scénario courant pour définir cette propriété est lors d’une liaison à des propriétés de source qui peuvent être `null` dans une collection liée.

La page **Monkeys** illustre la définition de la propriété [`TargetNullValue`](xref:Xamarin.Forms.BindingBase.TargetNullValue) :

```xaml
<ListView ItemsSource="{Binding Monkeys}"
          ...>
    <ListView.ItemTemplate>
        <DataTemplate>
            <ViewCell>
                <Grid>
                    ...
                    <Image Source="{Binding ImageUrl, TargetNullValue='https://upload.wikimedia.org/wikipedia/commons/2/20/Point_d_interrogation.jpg'}"
                           ... />
                    ...
                    <Label Text="{Binding Location, TargetNullValue='Location unknown'}"
                           ... />
                </Grid>
            </ViewCell>
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView>
```

Les liaisons sur le [`Image`](xref:Xamarin.Forms.Image) et [`Label`](xref:Xamarin.Forms.Label) définissent les deux [`TargetNullValue`](xref:Xamarin.Forms.BindingBase.TargetNullValue) valeurs qui seront appliquées si le chemin de liaison retourne `null` . Par conséquent, les valeurs définies par les propriétés `TargetNullValue` seront affichées pour tous les objets dans la collection où les propriétés `ImageUrl` et `Location` ne sont pas définies. Notez qu’ici les valeurs de propriété `TargetNullValue` sont délimitées par des guillemets simples (apostrophes).

Plutôt que de définir [`TargetNullValue`](xref:Xamarin.Forms.BindingBase.TargetNullValue) des valeurs de propriété en ligne, il est recommandé de les définir en tant que ressources dans un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) . L’avantage de cette approche est que ces valeurs sont définies une seule fois dans un emplacement unique et sont plus facilement localisables. Les ressources peuvent ensuite être récupérées à l’aide de l’extension de balisage `StaticResource` :

```xaml
<Image Source="{Binding ImageUrl, TargetNullValue={StaticResource fallbackImageUrl}}"
       ... />
<Label Text="{Binding Location, TargetNullValue={StaticResource locationUnknown}}"
       ... />
```

> [!NOTE]
> Il n’est pas possible de définir la propriété `TargetNullValue` avec une expression de liaison.

Voici le programme en cours d’exécution :

[![Liaison TargetNullValue](binding-fallbacks-images/bindingunavailable-small.png "Liaison TargetNullValue")](binding-fallbacks-images/bindingunavailable-large.png#lightbox "Liaison TargetNullValue")

Lorsque la propriété `TargetNullValue` n’est pas définie dans une expression de liaison, une valeur source `null` est convertie si un convertisseur de valeurs est défini, formatée si un `StringFormat` est défini, et le résultat est ensuite défini sur la cible. Toutefois, lorsque la propriété `TargetNullValue` est définie, une valeur source `null` est convertie si un convertisseur de valeurs est défini, et si elle est encore `null` après la conversion, la valeur de la propriété `TargetNullValue` est définie sur la cible.

> [!IMPORTANT]
> Le formatage de chaîne n’est pas appliqué dans une expression de liaison lorsque la propriété `TargetNullValue` est définie.

## <a name="related-links"></a>Liens connexes

- [Démonstrations de liaison de données (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)
