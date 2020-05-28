---
titre : ' ' Xamarin.Essentials : Dépannage' 'Description : 'ce document décrit comment résoudre les problèmes rencontrés lors du développement avec la Xamarin.Essentials bibliothèque. '
ms. AssetID : Auteur : ms. Author : ms. Date : No-Loc :
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

---

# <a name="xamarinessentials-troubleshooting"></a>Xamarin.Essentials: Résolution des problèmes

## <a name="error-version-conflict-detected-for-xamarinandroidsupportcompat"></a>Erreur : Conflit de version détecté pour Xamarin.Android.Support.Compat

L’erreur suivante peut se produire lors de la mise à jour de packages NuGet (ou l’ajout d’un nouveau package) avec unXamarin.Forms
projet qui utilise Xamarin.Essentials :

```error
NU1107: Version conflict detected for Xamarin.Android.Support.Compat. Reference the package directly from the project to resolve this issue.
 MyApp -> Xamarin.Essentials 1.3.1 -> Xamarin.Android.Support.CustomTabs 28.0.0.3 -> Xamarin.Android.Support.Compat (= 28.0.0.3)
 MyApp -> Xamarin.Forms 3.1.0.583944 -> Xamarin.Android.Support.v4 25.4.0.2 -> Xamarin.Android.Support.Compat (= 25.4.0.2).
```

Le problème se rapporte à des dépendances incompatibles pour les deux packages NuGet. Pour résoudre ce problème, vous pouvez ajouter manuellement une version spécifique de la dépendance (dans ce cas **Xamarin.Android.Support.Compat**) capable de prendre en charge les deux.

Pour ce faire, ajoutez manuellement le package NuGet qui est la source du conflit et utilisez la liste **Version** pour sélectionner une version spécifique. Actuellement, la version 28.0.0.3 du Xamarin. Android. support. compat & Xamarin. Android. support. Core. util NuGet permet de résoudre cette erreur.

Reportez-vous à [ce billet de blog](https://redth.codes/how-to-fix-the-dreaded-version-conflict-nuget-error-in-your-xamarin-android-projects/) pour plus d’informations et une vidéo sur la façon de résoudre le problème.

Si vous rencontrez des problèmes ou si vous trouvez un bogue, signalez-le sur le [ Xamarin.Essentials référentiel GitHub](https://github.com/xamarin/Essentials).
