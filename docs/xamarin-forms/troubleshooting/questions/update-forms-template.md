---
titre : « puis-je mettre à jour le Xamarin.Forms modèle par défaut vers un package NuGet plus récent ? »
ms. topic : Troubleshooting ms. Prod : xamarin ms. AssetID : 160FBE13-26EB-4B4F-9248-A5CBE58FDD7F ms. Technology : xamarin-Forms Author : davidbritch ms. Author : dabritch ms. Date : 04/25/2017 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="can-i-update-the-xamarinforms-default-template-to-a-newer-nuget-package"></a>Puis-je mettre à jour le Xamarin.Forms modèle par défaut vers un package NuGet plus récent ?

Ce guide utilise le Xamarin.Forms modèle de bibliothèque .NET standard comme exemple, mais la même méthode générale fonctionne également pour le Xamarin.Forms modèle de projet partagé. Ce guide est écrit avec l’exemple de mise à jour de Xamarin.Forms 1.5.1.6471 vers 2.1.0.6529, mais les mêmes étapes sont possibles pour définir d’autres versions comme valeurs par défaut à la place.

1. Copiez le modèle d’origine `.zip` à partir de :

    > `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Xamarin\Xamarin\[Xamarin Version]\T\PT\Cross-Platform\Xamarin.Forms.PCL.zip`

2. Décompressez le `.zip` vers un emplacement temporaire.

3. Remplacez toutes les occurrences de l’ancienne version du Xamarin.Forms package par la nouvelle version que vous souhaitez utiliser.
    * `FormsTemplate\FormsTemplate.vstemplate`
    * `FormsTemplate.Android\FormsTemplate.Android.vstemplate`
    * `FormsTemplate.iOS\FormsTemplate.iOS.vstemplate`

    Exemple : `<package id="Xamarin.Forms" version="1.5.1.6471" />` -> `<package id="Xamarin.Forms" version="2.1.0.6529" />`

4. Modifiez l’élément « Name » du fichier de [modèle à plusieurs projets](https://msdn.microsoft.com/library/ms185308.aspx) principal ( `Xamarin.Forms.PCL.vstemplate` ) pour le rendre unique. Par exemple :

    > `<Name>Blank App (Xamarin.Forms Portable) - 2.1.0.6529</Name>`

5. Ré-compresser le dossier de modèles dans son intégralité. Assurez-vous qu’elle correspond à la structure de fichier d’origine du `.zip` fichier. Le `Xamarin.Forms.PCL.vstemplate` fichier doit se trouver en haut du `.zip` fichier, et non dans un dossier.

6. Créez un sous-répertoire « Mobile Apps » dans votre dossier de modèles Visual Studio par utilisateur :
    > `%USERPROFILE%\Documents\Visual Studio 2013\Templates\ProjectTemplates\Visual C#\Mobile Apps`

7. Copiez le nouveau dossier de modèle compressé dans le nouveau répertoire « Mobile Apps ».

8. Téléchargez le package NuGet qui correspond à la version de l’étape 3. Par exemple, [ https://nuget.org/api/v2/package/ Xamarin.Forms /2.1.0.6529](https://nuget.org/api/v2/package/Xamarin.Forms/2.1.0.6529) (voir aussi [https://stackoverflow.com/questions/8597375/how-to-get-the-url-of-a-nupkg-file](https://stackoverflow.com/questions/8597375/how-to-get-the-url-of-a-nupkg-file) ), puis copiez-le dans le sous-dossier approprié du dossier d’extensions Visual Studio Xamarin :
    > `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Xamarin\Xamarin\[Xamarin Version]\Packages`
