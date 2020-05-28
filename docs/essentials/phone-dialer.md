---
titre : « » Xamarin.Essentials : le Numéroteur téléphonique «Description : » la classe PhoneDialer dans Xamarin.Essentials permet à une application d’ouvrir un numéro de téléphone dans le numéroteur «ms. AssetID : Author : ms. Custom : ms. Author : ms. Date : No-Loc :
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

---

# <a name="xamarinessentials-phone-dialer"></a>Xamarin.Essentials: Numéroteur téléphonique

La classe **PhoneDialer** permet à une application de composer un numéro de téléphone sur le clavier.

## <a name="get-started"></a>Prise en main

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-phone-dialer"></a>Utilisation du clavier téléphonique

Ajoutez une référence à Xamarin.Essentials dans votre classe :

```csharp
using Xamarin.Essentials;
```

La fonctionnalité de clavier téléphonique appelle la méthode `Open` pour composer un numéro de téléphone sur le clavier. Quand `Open` est demandé, l’API tente automatiquement d’appliquer le format approprié au numéro en fonction de l’indicatif téléphonique international, s’il est spécifié.

```csharp
public class PhoneDialerTest
{
    public void PlacePhoneCall(string number)
    {
        try
        {
            PhoneDialer.Open(number);
        }
        catch (ArgumentNullException anEx)
        {
            // Number was null or white space
        }
        catch (FeatureNotSupportedException ex)
        {
            // Phone Dialer is not supported on this device.
        }
        catch (Exception ex)
        {
            // Other error has occurred.
        }
    }
}
```

## <a name="api"></a>API

- [Code source de la fonctionnalité de clavier téléphonique](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/PhoneDialer)
- [Documentation sur l’API de clavier téléphonique](xref:Xamarin.Essentials.PhoneDialer)

## <a name="related-video"></a>Vidéo associée

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Phone-Dialer-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
