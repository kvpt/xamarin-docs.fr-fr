---
title: Matrice d’erreurs Xamarin. Android
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 7EBE4C01-8EFC-4B7E-97BA-D879994F59AB
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/13/2018
ms.openlocfilehash: dd05acdf58fbae85d9d8a82e899ebedc4c945281
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73019604"
---
# <a name="xamarinandroid-errors-matrix"></a>Matrice d’erreurs Xamarin. Android

## <a name="errors-reference"></a>Référence des erreurs

Ce document fournit des informations sur les différents codes d’erreur de Xamarin.

|Category|Description|
|--- |--- |
|XA0xxx|Erreurs Android|
|XA1xxx|Erreurs de copie de fichier/liens symboliques (liées au projet)|
|XA2xxx|Erreurs de l'éditeur de liens|
|XA3xxx|Erreurs AOA|
|XA4xxx|Erreurs de génération de code|
|XA5xxx|Erreurs GCC et chaîne d’outils|
|XA6xxx|Erreurs d’outils internes Android|
|XA7xxx|Réservée|
|XA8xxx|Réservée|
|XA9xxx|Erreurs de licence|

## <a name="error-codes"></a>Codes d'erreur

### <a name="xa0xxx-errors"></a>Erreurs XA0xxx

|Code d'erreur|Description|
|--- |--- |
|XA0000|Erreur inattendue-veuillez remplir un [rapport de bogue](https://github.com/xamarin/xamarin-android/issues/new).|
|XA0001|«-devname a été fourni sans action spécifique à l’appareil.|
|XA0002|Impossible d’analyser la variable d’environnement'{0}'.|
|XA0003|Le nom d’application'{0}. exe’est en conflit avec un kit de développement logiciel (SDK) ou un nom d’assembly de produit (. dll).|
|XA0004|La nouvelle logique refcounting nécessite l’activation de SGen.|
|XA0005|Le répertoire de sortie'{0}'n’existe pas.|
|XA0006|Il n’existe aucune plateforme devel sur «{0}», utilisez--Platform = PLAT pour spécifier le kit de développement logiciel (SDK)|
|XA0007|L’assembly racine'{0}'n’existe pas.|
|XA0008|Vous devez fournir un assembly racine uniquement.|
|XA0009|Erreur lors du chargement des assemblys : {0}.|
|XA0010|Impossible d’analyser les arguments de ligne de commande : {0}.|
|XA0011|{0} a été généré par rapport à un Runtime plus récent ({1}) que les supports monotactiles.|
|XA0012|Des données incomplètes sont fournies pour terminer «{0}».|
|XA0013|La prise en charge du profilage requiert l’activation de SGen.|
|XA0014|iOS {0} ne prend pas en charge la création d’applications ciblant ARMv6.|
|XA0020|Impossible de déterminer le chemin d’accès Android.|
|XA0100|EmbeddedNativeLibrary'{0}'non valide dans le projet d’application Android. Utilisez AndroidNativeLibrary à la place.|

### <a name="xa1xxx-errors"></a>Erreurs XA1xxx

|Code d'erreur|Description|
|--- |--- |
|XA1001|Impossible de trouver une application dans le répertoire spécifié.|
|XA1002|Impossible de créer liens symboliques. les fichiers ont été copiés.|
|XA1003|Impossible de supprimer l’application'{0}'. Vous devrez peut-être arrêter l’application manuellement.|
|XA1004|Impossible d’accéder à la liste des applications installées.|
|XA1005|Impossible de supprimer l’application «{0}» sur l’appareil «{1}» : {2}. Vous devrez peut-être arrêter l’application manuellement.|
|XA1006|Impossible d’installer l’application «{0}» sur l’appareil «{1}» : {2}.|
|XA1007|Échec du lancement de l’application «{0}» sur l’appareil «{1}» : {2}. Vous pouvez toujours lancer l’application manuellement en appuyant dessus.|
|XA1008|Échec du lancement du simulateur : {0}.|
|XA1009|Impossible de copier l’assembly'{0}'dans'{1}' : {2}.|
|XA1010|Impossible de charger l’assembly'{0}' : {1}.|
|XA1011|Impossible d’ajouter le fichier de ressources manquant : '{0}'.|
|XA1101|Impossible de démarrer l’application.|
|XA1102|Impossible d’attacher à l’application (pour la supprimer) : {0}.|
|XA1103|Impossible de détacher.|
|XA1104|Échec de l’envoi du paquet : {0}.|
|XA1105|Type de réponse inattendu.|
|XA1106|Impossible d’obtenir la liste des applications sur l’appareil : délai d’attente de la demande dépassé.|
|XA1107|Échec du lancement de l’application.|
|XA1201|Impossible de charger le simulateur : {0}.|
|XA1301|La bibliothèque Native'{0}' ({1}) a été ignorée, car elle ne correspond pas à la ou aux architectures de build actuelles ({2}).|

### <a name="xa2xxx-errors"></a>Erreurs XA2xxx

|Code d'erreur|Description|
|--- |--- |
|XA2001|Impossible de lier les assemblys.|
|XA2002|Impossible de résoudre la référence : {0}.|
|XA2003|L’option'{0}'sera ignorée, car la liaison est désactivée.|
|XA2004|Le fichier de définitions de l’éditeur de liens supplémentaire'{0}'est introuvable.|
|XA2005|Les définitions de'{0}'n’ont pas pu être analysées.|
|XA2006|Impossible de résoudre la référence à l’élément de métadonnées «{0}» (défini dans «{1}») à partir de «{2}».|

### <a name="xa3xxx-errors"></a>Erreurs XA3xxx

Il s’agit d’erreurs AOA.

|Code d'erreur|Description|
|--- |--- |
|XA3001|Impossible d’AOA l’assembly'{0}'.|
|XA3002|Restriction AOA : la méthode'{0}'doit être statique, car elle est décorée avec [MonoPInvokeCallback].|
|XA3003|Les options--Debug et--LLVM sont en conflit. Le débogage réversible est désactivé.|

### <a name="xa4xxx-errors"></a>Erreurs XA4xxx

Il s’agit d’erreurs de génération de code.

|Code d'erreur|Description|
|--- |--- |
|XA4001|Le modèle principal n’a pas pu être ex«{0}».|
|XA4101|Le Bureau d’enregistrement ne peut pas générer de signature pour le type'{0}'.|
|XA4102|Le Bureau d’enregistrement a trouvé un type «{0}» non valide dans la signature de la méthode «{2}». Utilisez'{1}'à la place.|
|XA4103|Le Bureau d’enregistrement a trouvé un type non valide «{0}» dans la signature de la méthode «{2}» : le type implémente INativeObject, mais il n’a pas de constructeur qui accepte deux arguments (IntPtr, bool).|
|XA4104|Le Bureau d’enregistrement ne peut pas marshaler la valeur de retour pour le type'{0}'dans la signature pour la méthode'{1}'.|
|XA4105|Le Bureau d’enregistrement ne peut pas marshaler le paramètre de type'{0}'dans la signature pour la méthode'{1}'.|
|XA4106|Le Bureau d’enregistrement ne peut pas marshaler la valeur de retour pour la structure'{0}'dans la signature pour la méthode'{1}'.|
|XA4107|Le Bureau d’enregistrement ne peut pas marshaler le paramètre de type'{0}'dans la signature pour la méthode'{1}'.|
|XA4108|Le Bureau d’enregistrement ne peut pas obtenir le type ObjectiveC pour le type managé'{0}'.|
|XA4109|Échec de la compilation du code d’Registrar généré. Veuillez signaler un [rapport de bogue](https://bugzilla.xamarin.com).|
|XA4110|Le Bureau d’enregistrement ne peut pas marshaler le paramètre out de type'{0}'dans la signature pour la méthode'{1}'.|
|XA4111|Le Bureau d’enregistrement ne peut pas générer de signature pour le type'{0}'dans la méthode'{1}'.|
|XA4200|Peut uniquement générer des ACW pour les types’CLAAS'.|
|XA4201|Impossible de déterminer le nom JNI pour le type {0}.|
|XA4203|Le nom de type spécifié doit être complet.|
|XA4204|Impossible de résoudre le type d’interface'{0}'. Vérifiez qu'il ne manque aucune référence d'assembly.|
|XA4205|[ExportField] ne peut être utilisé que sur les méthodes avec 0 paramètre.|
|XA4206|[Export] ne peut pas être utilisé sur un type générique.|
|XA4207|[ExportField] ne peut pas être utilisé sur un type générique.|
|XA4208|[Java. Interop. ExportFieldAttribute] ne peut pas être utilisé sur une méthode qui retourne void.|
|XA4209|Échec de la création de JavaTypeInfo pour la classe : {0} en raison d' {1}.|
|XA4210|Vous devez ajouter une référence à mono. Android. Export. dll lorsque vous utilisez ExportAttribute ou ExportFieldAttribute.|
|XA4211|Fichier AndroidManifest. xml //uses-sdk/@android:targetSdkVersion '{0}'est inférieur à $ (TargetFrameworkVersion) '{1}'. Utilisation de{1} d’API pour la compilation ACW.|

### <a name="xa5xxx-errors"></a>Erreurs XA5xxx

|Code d'erreur|Description|
|--- |--- |
|XA5101|Compilateur'{0}'manquant. Installez Android NDK.|
|XA5102|Échec de la conversion de l’assembly en code natif. Veuillez signaler un [rapport de bogue](https://bugzilla.xamarin.com).|
|XA5103|Échec de la compilation du fichier'{0}'. Veuillez signaler un [rapport de bogue](https://bugzilla.xamarin.com).|
|XA5201|Échec de la liaison native. Consultez les indicateurs d’utilisateur fournis à gcc : {0}|
|XA5202|Échec de la liaison native. Consultez le journal de génération.|
|XA5303|Avertissement de liaison Native : {0}.|
|XA5300|Android SDK introuvable ou n’est pas entièrement installé.|
|XA5301|Outil « Strip » manquant. Installez le composant « outils en ligne de commande » Xcode.|
|XA5302|Outil’dsymutil’manquant. Installez le composant « outils en ligne de commande » Xcode.|
|XA5203|Échec de la génération des symboles de débogage (répertoire dSYM). Consultez le journal de génération.|
|XA5204|Impossible de supprimer le fichier binaire final. Consultez le journal de génération.|
|XA5205|Outil’AAPT’manquant. Installez le package d’outils de génération Android SDK.|
|XA5206|{0}., Le répertoire de ressources Android {1} n’existe pas.|
|XA5207|{0}., Le {1} de fichier de bibliothèque Java n’existe pas.|
|XA5208|Échec du téléchargement. Téléchargez {0} et placez-le dans le répertoire {1}.|
|XA5209|Échec de la décompression. Téléchargez {0} et extrayez-le dans le répertoire {1}.|
|XA5210|{0}., Le {1} de fichier de la bibliothèque Native n’existe pas.|

### <a name="xa6xxx-errors"></a>Erreurs XA6xxx

|Code d'erreur|Description|
|--- |--- |
|XA6001|L’exécution de la version de Cecil ne prend pas en charge la suppression d’assembly.|
|XA6002|Impossible de supprimer l’assembly'{0}'.|
|XA6003|Message UnauthorizedAccessException.|

### <a name="xa9xxx-errors"></a>Erreurs XA9xxx

Ces codes d’erreur sont les erreurs de licence et d’activation.

#### <a name="xa9000"></a>XA9000

 **Cause :** Licence expirée

 **Vérifié pendant :** Build

|Débutant|Indie|Professionnel (version d’évaluation)|Bureau|Entreprise|
|--- |--- |--- |--- |--- |
|WARNING|WARNING|WARNING|WARNING|WARNING|

#### <a name="xa9001"></a>XA9001

 **Cause :** Version d’évaluation expirée

 **Vérifié pendant :** Build

|Débutant|Indie|Professionnel (version d’évaluation)|Bureau|Entreprise|
|--- |--- |--- |--- |--- |
|WARNING|WARNING|WARNING|WARNING|WARNING|

#### <a name="xa9002"></a>XA9002

 **Cause :** AndroidJavaSource

 **Vérifié pendant :** Build

|Débutant|Indie|Professionnel (version d’évaluation)|Bureau|Entreprise|
|--- |--- |--- |--- |--- |
|ERROR|OK|OK|OK|OK|

 **Cause :** AndroidJavaLibrary

 **Vérifié pendant :** Build

|Débutant|Indie|Professionnel (version d’évaluation)|Bureau|Entreprise|
|--- |--- |--- |--- |--- |
|ERROR|OK|OK|OK|OK|

 **Si un assembly de liaison a le fichier. jar incorporé, il est intercepté au moment de l’exécution du package, et non pas au moment de la génération.**

 **Cause :** AndroidNativeLibrary

 **Vérifié pendant :** Build

|Débutant|Indie|Professionnel (version d’évaluation)|Bureau|Entreprise|
|--- |--- |--- |--- |--- |
|ERROR|OK|OK|OK|OK|

#### <a name="xa9003"></a>XA9003

 **Cause :** System. Runtime. Serialization

 **Vérifié pendant :** Packages

|Débutant|Indie|Professionnel (version d’évaluation)|Bureau|Entreprise|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|OK|OK|OK|

 **Cause :** System. ServiceModel. Web

 **Vérifié pendant :** Packages

|Débutant|Indie|Professionnel (version d’évaluation)|Bureau|Entreprise|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|OK|OK|OK|

 **Cause :** Mono. Data. TDS

 **Vérifié pendant :** Build

|Débutant|Indie|Professionnel (version d’évaluation)|Bureau|Entreprise|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|OK|OK|OK|

 **Elle est référencée par System. Data. dll, ce qui est autorisé**

 **Cause :** Mono. Android. Export

 **Vérifié pendant :** Build

|Débutant|Indie|Professionnel (version d’évaluation)|Bureau|Entreprise|
|--- |--- |--- |--- |--- |
|ERROR|OK|OK|OK|OK|

#### <a name="xa9004"></a>XA9004

 **Cause :** --profilage

 **Vérifié pendant :** Packages

|Débutant|Indie|Professionnel (version d’évaluation)|Bureau|Entreprise|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|OK|OK|OK|

#### <a name="xa9005"></a>XA9005

 **Cause :** Limite de taille (32ko).

 **Vérifié pendant :** Packages

|Débutant|Indie|Professionnel (version d’évaluation)|Bureau|Entreprise|
|--- |--- |--- |--- |--- |
|ERROR|OK|-|-|-|

#### <a name="xa9006"></a>XA9006

 **Cause :** Espace de noms System. Data. SqlClient.

 **Vérifié pendant :** Packages

|Débutant|Indie|Professionnel (version d’évaluation)|Bureau|Entreprise|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|OK|OK|OK|

#### <a name="xa9008"></a>XA9008

 **Cause :** Génération à partir de la ligne de commande.

 **Vérifié pendant :** Build

|Débutant|Indie|Professionnel (version d’évaluation)|Bureau|Entreprise|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|OK|OK|OK|

#### <a name="xa9009"></a>XA9009

 **Cause :** Numéro de série manquant.

 **Vérifié pendant :** Ne pas être testé

|Débutant|Indie|Professionnel (version d’évaluation)|Bureau|Entreprise|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|ERROR|ERROR|ERROR|

#### <a name="xa9010"></a>XA9010

 **Cause :** ID de produit non valide.

 **Vérifié pendant :** Build

|Débutant|Indie|Professionnel (version d’évaluation)|Bureau|Entreprise|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|ERROR|ERROR|ERROR|

Équivalent à XA9018.

#### <a name="xa9011"></a>XA9011

 **Cause :** Échec de la mise à jour du fichier de licence (dans un nouveau format de fichier).

 **Vérifié pendant :** Actionne

|Débutant|Indie|Professionnel (version d’évaluation)|Bureau|Entreprise|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|ERROR|ERROR|ERROR|

#### <a name="xa9012"></a>XA9012

 **Cause :** Aucun Internet

 **Vérifié pendant :** Actionne

|Débutant|Indie|Professionnel (version d’évaluation)|Bureau|Entreprise|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|ERROR|ERROR|ERROR|

#### <a name="xa9013"></a>XA9013

 **Cause :** Erreur inconnue

 **Vérifié pendant :** Actionne

|Débutant|Indie|Professionnel (version d’évaluation)|Bureau|Entreprise|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|ERROR|ERROR|ERROR|

#### <a name="xa9014"></a>XA9014

 **Cause :** Code d’activation non valide

 **Vérifié pendant :** Actionne

|Débutant|Indie|Professionnel (version d’évaluation)|Bureau|Entreprise|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|ERROR|ERROR|ERROR|

#### <a name="xa9017"></a>XA9017

 **Cause :** Le serveur d’activation ne retourne pas une licence valide.

 **Vérifié pendant :** Actionne

|Débutant|Indie|Professionnel (version d’évaluation)|Bureau|Entreprise|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|ERROR|ERROR|ERROR|

#### <a name="xa9018"></a>XA9018

**Cause :** Licence non valide

**Vérifié pendant :** Build

|Débutant|Indie|Professionnel (version d’évaluation)|Bureau|Entreprise|
|--- |--- |--- |--- |--- |
|-|-|ERROR|-|-|
