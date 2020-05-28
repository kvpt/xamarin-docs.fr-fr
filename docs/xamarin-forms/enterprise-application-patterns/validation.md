---
title: ''
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 4a9af91e2d48ba7ef7fdcdb4f8472e0aaafb7854
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84138708"
---
# <a name="validation-in-enterprise-apps"></a>Validation dans les applications d’entreprise

Toute application qui accepte les entrées d’utilisateurs doit s’assurer que l’entrée est valide. Une application peut, par exemple, Rechercher des entrées qui contiennent uniquement des caractères dans une plage particulière, qui est d’une certaine longueur ou qui correspond à un format particulier. Sans validation, un utilisateur peut fournir des données qui provoquent l’échec de l’application. La validation applique des règles d’entreprise et empêche une personne malveillante d’injecter des données malveillantes.

Dans le contexte du modèle MVVM (Model-View-ViewModel), un modèle de vue ou un modèle est souvent requis pour effectuer la validation des données et signaler toutes les erreurs de validation à la vue afin que l’utilisateur puisse les corriger. L’application mobile eShopOnContainers effectue une validation synchrone côté client des propriétés de modèle de vue et avertit l’utilisateur des erreurs de validation en mettant en surbrillance le contrôle qui contient les données non valides et en affichant les messages d’erreur qui informent l’utilisateur de la raison pour laquelle les données ne sont pas valides. La figure 6-1 montre les classes impliquées dans l’exécution de la validation dans l’application mobile eShopOnContainers.

[![](validation-images/validation.png "Validation classes in the eShopOnContainers mobile app")](validation-images/validation-large.png#lightbox "Validation classes in the eShopOnContainers mobile app")

**Figure 6-1**: classes de validation dans l’application mobile eShopOnContainers

Les propriétés de modèle de vue qui nécessitent une validation sont de type `ValidatableObject<T>` , et chaque `ValidatableObject<T>` instance a des règles de validation ajoutées à sa `Validations` propriété. La validation est appelée à partir du modèle de vue en appelant la `Validate` méthode de l' `ValidatableObject<T>` instance, qui récupère les règles de validation et les exécute sur la `ValidatableObject<T>` `Value` propriété. Toutes les erreurs de validation sont placées dans la `Errors` propriété de l' `ValidatableObject<T>` instance et la `IsValid` propriété de l' `ValidatableObject<T>` instance est mise à jour pour indiquer si la validation a réussi ou échoué.

La notification de modification de propriété est fournie par la `ExtendedBindableObject` classe, ce qui signifie qu’un [`Entry`](xref:Xamarin.Forms.Entry) contrôle peut effectuer une liaison à la `IsValid` propriété de l' `ValidatableObject<T>` instance dans la classe de modèle de vue pour être informé de la validité ou non des données entrées.

## <a name="specifying-validation-rules"></a>Spécification des règles de validation

Les règles de validation sont spécifiées en créant une classe qui dérive de l' `IValidationRule<T>` interface, qui est illustrée dans l’exemple de code suivant :

```csharp
public interface IValidationRule<T>  
{  
    string ValidationMessage { get; set; }  
    bool Check(T value);  
}
```

Cette interface spécifie qu’une classe de règles de validation doit fournir une `boolean` `Check` méthode utilisée pour effectuer la validation requise, et une `ValidationMessage` propriété dont la valeur est le message d’erreur de validation qui s’affiche en cas d’échec de la validation.

L’exemple de code suivant montre la `IsNotNullOrEmptyRule<T>` règle de validation, qui est utilisée pour effectuer la validation du nom d’utilisateur et du mot de passe entrés par l’utilisateur lors de l' `LoginView` utilisation de services fictifs dans l’application mobile eShopOnContainers :

```csharp
public class IsNotNullOrEmptyRule<T> : IValidationRule<T>  
{  
    public string ValidationMessage { get; set; }  

    public bool Check(T value)  
    {  
        if (value == null)  
        {  
            return false;  
        }  

        var str = value as string;  
        return !string.IsNullOrWhiteSpace(str);  
    }  
}
```

La `Check` méthode retourne une `boolean` valeur qui indique si l’argument `null` de valeur est, vide ou se compose uniquement de caractères d’espace blanc.

Bien qu’il ne soit pas utilisé par l’application mobile eShopOnContainers, l’exemple de code suivant montre une règle de validation pour la validation des adresses de messagerie :

```csharp
public class EmailRule<T> : IValidationRule<T>  
{  
    public string ValidationMessage { get; set; }  

    public bool Check(T value)  
    {  
        if (value == null)  
        {  
            return false;  
        }  

        var str = value as string;  
        Regex regex = new Regex(@"^([\w\.\-]+)@([\w\-]+)((\.(\w){2,3})+)$");  
        Match match = regex.Match(str);  

        return match.Success;  
    }  
}
```

La `Check` méthode retourne une `boolean` valeur indiquant si l’argument de valeur est une adresse de messagerie valide. Pour cela, il faut Rechercher l’argument de valeur pour la première occurrence du modèle d’expression régulière spécifié dans le `Regex` constructeur. Vous pouvez déterminer si le modèle d’expression régulière a été trouvé dans la chaîne d’entrée en vérifiant la valeur de la propriété de l' `Match` objet `Success` .

> [!NOTE]
> La validation de propriété peut impliquer parfois des propriétés dépendantes. Un exemple de propriétés dépendantes est lorsque l’ensemble de valeurs valides pour la propriété A dépend de la valeur particulière qui a été définie dans la propriété B. Pour vérifier que la valeur de la propriété A est l’une des valeurs autorisées implique la récupération de la valeur de la propriété B. En outre, lorsque la valeur de la propriété B change, la propriété A doit être revalidée.

## <a name="adding-validation-rules-to-a-property"></a>Ajout de règles de validation à une propriété

Dans l’application mobile eShopOnContainers, les propriétés de modèle qui nécessitent une validation sont déclarées comme étant de type `ValidatableObject<T>` , où `T` est le type des données à valider. L’exemple de code suivant montre deux propriétés de ce type :

```csharp
public ValidatableObject<string> UserName  
{  
    get  
    {  
        return _userName;  
    }  
    set  
    {  
        _userName = value;  
        RaisePropertyChanged(() => UserName);  
    }  
}  

public ValidatableObject<string> Password  
{  
    get  
    {  
        return _password;  
    }  
    set  
    {  
        _password = value;  
        RaisePropertyChanged(() => Password);  
    }  
}
```

Pour que la validation se produise, des règles de validation doivent être ajoutées à la `Validations` collection de chaque `ValidatableObject<T>` instance, comme illustré dans l’exemple de code suivant :

```csharp
private void AddValidations()  
{  
    _userName.Validations.Add(new IsNotNullOrEmptyRule<string>   
    {   
        ValidationMessage = "A username is required."   
    });  
    _password.Validations.Add(new IsNotNullOrEmptyRule<string>   
    {   
        ValidationMessage = "A password is required."   
    });  
}
```

Cette méthode ajoute la `IsNotNullOrEmptyRule<T>` règle de validation à la `Validations` collection de chaque `ValidatableObject<T>` instance, en spécifiant des valeurs pour la propriété de la règle de validation `ValidationMessage` , qui spécifie le message d’erreur de validation qui s’affichera en cas d’échec de la validation.

## <a name="triggering-validation"></a>Déclenchement de la validation

L’approche de validation utilisée dans l’application mobile eShopOnContainers peut déclencher manuellement la validation d’une propriété et déclencher automatiquement la validation lorsqu’une propriété change.

### <a name="triggering-validation-manually"></a>Déclenchement manuel de la validation

La validation peut être déclenchée manuellement pour une propriété de modèle de vue. Par exemple, cela se produit dans l’application mobile eShopOnContainers quand l’utilisateur appuie sur le bouton de **connexion** sur le `LoginView` , lors de l’utilisation de services fictifs. Le délégué de commande appelle la `MockSignInAsync` méthode dans le `LoginViewModel` , qui appelle la validation en exécutant la `Validate` méthode, qui est illustrée dans l’exemple de code suivant :

```csharp
private bool Validate()  
{  
    bool isValidUser = ValidateUserName();  
    bool isValidPassword = ValidatePassword();  
    return isValidUser && isValidPassword;  
}  

private bool ValidateUserName()  
{  
    return _userName.Validate();  
}  

private bool ValidatePassword()  
{  
    return _password.Validate();  
}
```

La `Validate` méthode effectue la validation du nom d’utilisateur et du mot de passe entrés par l’utilisateur sur le `LoginView` , en appelant la méthode Validate sur chaque `ValidatableObject<T>` instance. L’exemple de code suivant montre la méthode Validate de la `ValidatableObject<T>` classe :

```csharp
public bool Validate()  
{  
    Errors.Clear();  

    IEnumerable<string> errors = _validations  
        .Where(v => !v.Check(Value))  
        .Select(v => v.ValidationMessage);  

    Errors = errors.ToList();  
    IsValid = !Errors.Any();  

    return this.IsValid;  
}
```

Cette méthode efface la `Errors` collection, puis récupère toutes les règles de validation qui ont été ajoutées à la collection de l’objet `Validations` . La `Check` méthode pour chaque règle de validation Récupérée est exécutée, et la `ValidationMessage` valeur de la propriété pour toute règle de validation qui ne parvient pas à valider les données est ajoutée à la `Errors` collection de l' `ValidatableObject<T>` instance. Enfin, la `IsValid` propriété est définie et sa valeur est retournée à la méthode d’appel, indiquant si la validation a réussi ou échoué.

### <a name="triggering-validation-when-properties-change"></a>Déclenchement de la validation lors de la modification des propriétés

La validation peut également être déclenchée chaque fois qu’une propriété liée change. Par exemple, lorsqu’une liaison bidirectionnelle dans le `LoginView` définit la `UserName` `Password` propriété ou, la validation est déclenchée. L’exemple de code suivant montre comment cela se produit :

```xaml
<Entry Text="{Binding UserName.Value, Mode=TwoWay}">  
    <Entry.Behaviors>  
        <behaviors:EventToCommandBehavior  
            EventName="TextChanged"  
            Command="{Binding ValidateUserNameCommand}" />  
    </Entry.Behaviors>  
    ...  
</Entry>
```

Le [`Entry`](xref:Xamarin.Forms.Entry) contrôle est lié à la `UserName.Value` propriété de l' `ValidatableObject<T>` instance et `Behaviors` une instance est ajoutée à la collection du contrôle `EventToCommandBehavior` . Ce comportement exécute le `ValidateUserNameCommand` en réponse au déclenchement de l' `TextChanged` événement [] sur le `Entry` , qui est déclenché lorsque le texte du est `Entry` modifié. Le `ValidateUserNameCommand` délégué exécute ensuite la `ValidateUserName` méthode, qui exécute la `Validate` méthode sur l' `ValidatableObject<T>` instance de. Par conséquent, chaque fois que l’utilisateur entre un caractère dans le `Entry` contrôle pour le nom d’utilisateur, la validation des données entrées est effectuée.

Pour plus d’informations sur les comportements, consultez [implémentation de comportements](~/xamarin-forms/enterprise-application-patterns/mvvm.md#implementing_behaviors).

<a name="displaying_validation_errors" />

## <a name="displaying-validation-errors"></a>Affichage des erreurs de validation

L’application mobile eShopOnContainers informe l’utilisateur des erreurs de validation en mettant en surbrillance le contrôle qui contient les données non valides avec une ligne rouge et en affichant un message d’erreur qui informe l’utilisateur que les données ne sont pas valides sous le contrôle contenant les données non valides. Lorsque les données non valides sont corrigées, la ligne devient noire et le message d’erreur est supprimé. La figure 6-2 montre le LoginView dans l’application mobile eShopOnContainers lorsque des erreurs de validation sont présentes.

![](validation-images/validation-login.png "Displaying validation errors during login")

**Figure 6-2 :** Affichage des erreurs de validation pendant la connexion

### <a name="highlighting-a-control-that-contains-invalid-data"></a>Mise en surbrillance d’un contrôle qui contient des données non valides

Le `LineColorBehavior` comportement attaché est utilisé pour mettre en surbrillance [`Entry`](xref:Xamarin.Forms.Entry) des contrôles où des erreurs de validation se sont produites. L’exemple de code suivant montre comment le `LineColorBehavior` comportement attaché est attaché à un `Entry` contrôle :

```xaml
<Entry Text="{Binding UserName.Value, Mode=TwoWay}">
    <Entry.Style>
        <OnPlatform x:TypeArguments="Style">
            <On Platform="iOS, Android" Value="{StaticResource EntryStyle}" />
            <On Platform="UWP" Value="{StaticResource UwpEntryStyle}" />
        </OnPlatform>
    </Entry.Style>
    ...
</Entry>
```

Le [`Entry`](xref:Xamarin.Forms.Entry) contrôle consomme un style explicite, qui est illustré dans l’exemple de code suivant :

```xaml
<Style x:Key="EntryStyle"  
       TargetType="{x:Type Entry}">  
    ...  
    <Setter Property="behaviors:LineColorBehavior.ApplyLineColor"  
            Value="True" />  
    <Setter Property="behaviors:LineColorBehavior.LineColor"  
            Value="{StaticResource BlackColor}" />  
    ...  
</Style>
```

Ce style définit les `ApplyLineColor` `LineColor` propriétés jointes et du `LineColorBehavior` comportement attaché sur le [`Entry`](xref:Xamarin.Forms.Entry) contrôle. Pour plus d’informations sur les styles, consultez [Styles](~/xamarin-forms/user-interface/styles/index.md).

Lorsque la valeur de la `ApplyLineColor` propriété jointe est définie, ou change, le `LineColorBehavior` comportement attaché exécute la `OnApplyLineColorChanged` méthode, qui est illustrée dans l’exemple de code suivant :

```csharp
public static class LineColorBehavior  
{  
    ...  
    private static void OnApplyLineColorChanged(  
                BindableObject bindable, object oldValue, object newValue)  
    {  
        var view = bindable as View;  
        if (view == null)  
        {  
            return;  
        }  

        bool hasLine = (bool)newValue;  
        if (hasLine)  
        {  
            view.Effects.Add(new EntryLineColorEffect());  
        }  
        else  
        {  
            var entryLineColorEffectToRemove =   
                    view.Effects.FirstOrDefault(e => e is EntryLineColorEffect);  
            if (entryLineColorEffectToRemove != null)  
            {  
                view.Effects.Remove(entryLineColorEffectToRemove);  
            }  
        }  
    }  
}
```

Les paramètres de cette méthode fournissent l’instance du contrôle auquel le comportement est attaché, ainsi que les anciennes et nouvelles valeurs de la `ApplyLineColor` propriété jointe. La `EntryLineColorEffect` classe est ajoutée à la collection du contrôle [`Effects`](xref:Xamarin.Forms.Element.Effects) si la `ApplyLineColor` propriété jointe est `true` , sinon elle est supprimée de la `Effects` collection du contrôle. Pour plus d’informations sur les comportements, consultez [implémentation de comportements](~/xamarin-forms/enterprise-application-patterns/mvvm.md#implementing_behaviors).

La `EntryLineColorEffect` sous-classe la [`RoutingEffect`](xref:Xamarin.Forms.RoutingEffect) classe et est illustrée dans l’exemple de code suivant :

```csharp
public class EntryLineColorEffect : RoutingEffect  
{  
    public EntryLineColorEffect() : base("eShopOnContainers.EntryLineColorEffect")  
    {  
    }  
}
```

La [`RoutingEffect`](xref:Xamarin.Forms.RoutingEffect) classe représente un effet indépendant de la plateforme qui encapsule un effet interne qui est spécifique à la plateforme. Cela simplifie le processus de suppression de l’effet, car il n’y a pas d’accès au moment de la compilation aux informations de type pour un effet propre à la plateforme. Le `EntryLineColorEffect` appelle le constructeur de classe de base, en passant un paramètre qui se compose d’une concaténation du nom du groupe de résolutions et de l’ID unique spécifié sur chaque classe d’effet spécifique à la plateforme.

L’exemple de code suivant montre l' `eShopOnContainers.EntryLineColorEffect` implémentation pour iOS :

```csharp
[assembly: ResolutionGroupName("eShopOnContainers")]  
[assembly: ExportEffect(typeof(EntryLineColorEffect), "EntryLineColorEffect")]  
namespace eShopOnContainers.iOS.Effects  
{  
    public class EntryLineColorEffect : PlatformEffect  
    {  
        UITextField control;  

        protected override void OnAttached()  
        {  
            try  
            {  
                control = Control as UITextField;  
                UpdateLineColor();  
            }  
            catch (Exception ex)  
            {  
                Console.WriteLine("Can't set property on attached control. Error: ", ex.Message);  
            }  
        }  

        protected override void OnDetached()  
        {  
            control = null;  
        }  

        protected override void OnElementPropertyChanged(PropertyChangedEventArgs args)  
        {  
            base.OnElementPropertyChanged(args);  

            if (args.PropertyName == LineColorBehavior.LineColorProperty.PropertyName ||  
                args.PropertyName == "Height")  
            {  
                Initialize();  
                UpdateLineColor();  
            }  
        }  

        private void Initialize()  
        {  
            var entry = Element as Entry;  
            if (entry != null)  
            {  
                Control.Bounds = new CGRect(0, 0, entry.Width, entry.Height);  
            }  
        }  

        private void UpdateLineColor()  
        {  
            BorderLineLayer lineLayer = control.Layer.Sublayers.OfType<BorderLineLayer>()  
                                                             .FirstOrDefault();  

            if (lineLayer == null)  
            {  
                lineLayer = new BorderLineLayer();  
                lineLayer.MasksToBounds = true;  
                lineLayer.BorderWidth = 1.0f;  
                control.Layer.AddSublayer(lineLayer);  
                control.BorderStyle = UITextBorderStyle.None;  
            }  

            lineLayer.Frame = new CGRect(0f, Control.Frame.Height-1f, Control.Bounds.Width, 1f);  
            lineLayer.BorderColor = LineColorBehavior.GetLineColor(Element).ToCGColor();  
            control.TintColor = control.TextColor;  
        }  

        private class BorderLineLayer : CALayer  
        {  
        }  
    }  
}
```

La `OnAttached` méthode récupère le contrôle natif pour le Xamarin.Forms [`Entry`](xref:Xamarin.Forms.Entry) contrôle et met à jour la couleur de ligne en appelant la `UpdateLineColor` méthode. Le `OnElementPropertyChanged` remplacement répond aux modifications de propriétés pouvant être liées sur le `Entry` contrôle en mettant à jour la couleur de ligne si la propriété jointe `LineColor` change ou la [`Height`](xref:Xamarin.Forms.VisualElement.Height) propriété de la `Entry` modification. Pour plus d’informations sur les effets, consultez [Effets](~/xamarin-forms/app-fundamentals/effects/index.md).

Quand des données valides sont entrées dans le [`Entry`](xref:Xamarin.Forms.Entry) contrôle, elles appliquent une ligne noire en bas du contrôle, pour indiquer qu’il n’y a aucune erreur de validation. La figure 6-3 illustre un exemple.

![](validation-images/validation-blackline.png "Black line indicating no validation error")

**Figure 6-3**: ligne noire indiquant l’absence d’erreur de validation

Le [`Entry`](xref:Xamarin.Forms.Entry) contrôle a également un [`DataTrigger`](xref:Xamarin.Forms.DataTrigger) ajouté à sa [`Triggers`](xref:Xamarin.Forms.VisualElement.Triggers) collection. L’exemple de code suivant montre les `DataTrigger` éléments suivants :

```xaml
<Entry Text="{Binding UserName.Value, Mode=TwoWay}">  
    ...  
    <Entry.Triggers>  
        <DataTrigger   
            TargetType="Entry"  
            Binding="{Binding UserName.IsValid}"  
            Value="False">  
            <Setter Property="behaviors:LineColorBehavior.LineColor"   
                    Value="{StaticResource ErrorColor}" />  
        </DataTrigger>  
    </Entry.Triggers>  
</Entry>
```

Cela [`DataTrigger`](xref:Xamarin.Forms.DataTrigger) surveille la `UserName.IsValid` propriété et, si elle est valeur `false` , elle exécute le [`Setter`](xref:Xamarin.Forms.Setter) , qui modifie la `LineColor` propriété jointe du `LineColorBehavior` comportement attaché en rouge. La figure 6-4 illustre un exemple.

![](validation-images/validation-redline.png "Red line indicating validation error")

**Figure 6-4**: ligne rouge indiquant une erreur de validation

La ligne dans le [`Entry`](xref:Xamarin.Forms.Entry) contrôle reste en rouge alors que les données entrées ne sont pas valides. dans le cas contraire, elle devient noire pour indiquer que les données entrées sont valides.

Pour plus d’informations sur les déclencheurs, consultez [déclencheurs](~/xamarin-forms/app-fundamentals/triggers.md).

### <a name="displaying-error-messages"></a>Affichage des messages d’erreur

L’interface utilisateur affiche les messages d’erreur de validation dans les contrôles Label situés sous chaque contrôle dont les données n’ont pas été validées. L’exemple de code suivant affiche [`Label`](xref:Xamarin.Forms.Label) un message d’erreur de validation si l’utilisateur n’a pas entré un nom d’utilisateur valide :

```xaml
<Label Text="{Binding UserName.Errors, Converter={StaticResource FirstValidationErrorConverter}}"  
       Style="{StaticResource ValidationErrorLabelStyle}" />
```

Chaque [`Label`](xref:Xamarin.Forms.Label) lie à la `Errors` propriété de l’objet de modèle de vue qui est en cours de validation. La `Errors` propriété est fournie par la `ValidatableObject<T>` classe et est de type `List<string>` . Étant donné que la `Errors` propriété peut contenir plusieurs erreurs de validation, l' `FirstValidationErrorConverter` instance est utilisée pour récupérer la première erreur de la collection en vue de son affichage.

## <a name="summary"></a>Résumé

L’application mobile eShopOnContainers effectue une validation synchrone côté client des propriétés de modèle de vue et avertit l’utilisateur des erreurs de validation en mettant en surbrillance le contrôle qui contient les données non valides et en affichant les messages d’erreur qui informent l’utilisateur de la raison pour laquelle les données ne sont pas valides.

Les propriétés de modèle de vue qui nécessitent une validation sont de type `ValidatableObject<T>` , et chaque `ValidatableObject<T>` instance a des règles de validation ajoutées à sa `Validations` propriété. La validation est appelée à partir du modèle de vue en appelant la `Validate` méthode de l' `ValidatableObject<T>` instance, qui récupère les règles de validation et les exécute sur la `ValidatableObject<T>` `Value` propriété. Toutes les erreurs de validation sont placées dans la `Errors` propriété de l' `ValidatableObject<T>` instance et la `IsValid` propriété de l' `ValidatableObject<T>` instance est mise à jour pour indiquer si la validation a réussi ou échoué.

## <a name="related-links"></a>Liens connexes

- [Télécharger le livre électronique (PDF de 2 Mo)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (exemple)](https://github.com/dotnet-architecture/eShopOnContainers)
