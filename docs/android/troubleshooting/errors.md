---
title: Matrice d’erreurs Xamarin. Android
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 7EBE4C01-8EFC-4B7E-97BA-D879994F59AB
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/13/2018
ms.openlocfilehash: 037b5a8939ab5866a10e7ecc58a1e2a5bdfb5bfd
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70757394"
---
# <a name="xamarinandroid-errors-matrix"></a>Matrice d’erreurs Xamarin. Android

## <a name="errors-reference"></a>Référence des erreurs

Ce document fournit des informations sur les différents codes d’erreur de Xamarin.

|Catégorie|Description|
|--- |--- |
|XA0xxx|Erreurs Android|
|XA1xxx|Erreurs de copie de fichier/liens symboliques (liées au projet)|
|XA2xxx|Erreurs de l'éditeur de liens|
|XA3xxx|Erreurs AOA|
|XA4xxx|Erreurs de génération de code|
|XA5xxx|Erreurs GCC et chaîne d’outils|
|XA6xxx|Erreurs d’outils internes Android|
|XA7xxx|Réservé|
|XA8xxx|Réservé|
|XA9xxx|Erreurs de licence|

## <a name="error-codes"></a>Codes d'erreur

### <a name="xa0xxx-errors"></a>Erreurs XA0xxx

|Code d'erreur|Description|
|--- |--- |
|XA0000|Erreur inattendue-veuillez remplir un [rapport de bogue](https://github.com/xamarin/xamarin-android/issues/new).|
|XA0001|«-devname a été fourni sans action spécifique à l’appareil.|
|XA0002|Impossible d’analyser la variable d’environnement{0}' '.|
|XA0003|Le nom d'{0}application'. exe’est en conflit avec un kit de développement logiciel (SDK) ou un nom d’assembly de produit (. dll).|
|XA0004|La nouvelle logique refcounting nécessite l’activation de SGen.|
|XA0005|Le répertoire de sortie{0}' 'n’existe pas.|
|XA0006|Il n’existe aucune plateforme devel sur{0}«», utilisez--Platform = plat pour spécifier le kit de développement logiciel (SDK)|
|XA0007|L’assembly racine{0}' 'n’existe pas.|
|XA0008|Vous devez fournir un assembly racine uniquement.|
|XA0009|Erreur lors du chargement des {0}assemblys :.|
|XA0010|Impossible d’analyser les arguments de ligne de {0}commande :.|
|XA0011|{0}a été généré par rapport à un Runtime{1}plus récent () que le support monotactile prend en charge.|
|XA0012|Des données incomplètes sont fournies{0}pour terminer «».|
|XA0013|La prise en charge du profilage requiert l’activation de SGen.|
|XA0014|iOS {0} ne prend pas en charge la création d’applications ciblant ARMv6.|
|XA0020|Impossible de déterminer le chemin d’accès Android.|
|XA0100|EmbeddedNativeLibrary «{0}» n’est pas valide dans le projet d’application Android. Utilisez AndroidNativeLibrary à la place.|

### <a name="xa1xxx-errors"></a>Erreurs XA1xxx

|Code d'erreur|Description|
|--- |--- |
|XA1001|Impossible de trouver une application dans le répertoire spécifié.|
|XA1002|Impossible de créer liens symboliques. les fichiers ont été copiés.|
|XA1003|Impossible de supprimer l’application'{0}'. Vous devrez peut-être arrêter l’application manuellement.|
|XA1004|Impossible d’accéder à la liste des applications installées.|
|XA1005|Impossible de supprimer l’application'{0}'sur l’appareil'{1}' : {2}. Vous devrez peut-être arrêter l’application manuellement.|
|XA1006|Impossible d’installer l’application'{0}'sur l’appareil'{1}' : {2}.|
|XA1007|Échec du lancement de l’application{0}' 'sur l’appareil{1}' ' {2}:. Vous pouvez toujours lancer l’application manuellement en appuyant dessus.|
|XA1008|Échec du lancement du simulateur {0}:.|
|XA1009|Impossible de copier l’assembly{0}' 'dans{1}' ' {2}:.|
|XA1010|Impossible de charger l’assembly{0}' ' {1}:.|
|XA1011|Impossible d’ajouter le fichier de ressources manquant{0}: ' '.|
|XA1101|Impossible de démarrer l’application.|
|XA1102|Impossible d’attacher à l’application (pour la supprimer) : {0}.|
|XA1103|Impossible de détacher.|
|XA1104|Échec de l’envoi du {0}paquet :.|
|XA1105|Type de réponse inattendu.|
|XA1106|Impossible d’accéder à la liste des applications sur l’appareil : Délai d’attente de la demande dépassé.|
|XA1107|Échec du lancement de l’application.|
|XA1201|Impossible de charger le simulateur {0}:.|
|XA1301|La bibliothèque native{0}«»{1}() a été ignorée, car elle ne correspond pas aux architectures de{2}Build actuelles ().|

### <a name="xa2xxx-errors"></a>Erreurs XA2xxx

|Code d'erreur|Description|
|--- |--- |
|XA2001|Impossible de lier les assemblys.|
|XA2002|Impossible de résoudre la référence {0}:.|
|XA2003|L’option{0}' 'sera ignorée, car la liaison est désactivée.|
|XA2004|Le fichier de définitions de l'{0}éditeur de liens supplémentaire «» est introuvable.|
|XA2005|Les définitions de{0}«» n’ont pas pu être analysées.|
|XA2006|La référence à l’élément{0}de métadonnées « »{1}(définie dans «{2}») à partir de «» n’a pas pu être résolue.|

### <a name="xa3xxx-errors"></a>Erreurs XA3xxx

Il s’agit d’erreurs AOA.

|Code d'erreur|Description|
|--- |--- |
|XA3001|Impossible d’AOA l’assembly{0}' '.|
|XA3002|Restriction AOA : La méthode{0}' 'doit être statique, car elle est décorée avec [MonoPInvokeCallback].|
|XA3003|Les options--Debug et--LLVM sont en conflit. Le débogage réversible est désactivé.|

### <a name="xa4xxx-errors"></a>Erreurs XA4xxx

Il s’agit d’erreurs de génération de code.

|Code d'erreur|Description|
|--- |--- |
|XA4001|Le modèle principal n’a pas pu être à l’arrêt{0}de «».|
|XA4101|Le Bureau d’enregistrement ne peut pas générer de{0}signature pour le type «».|
|XA4102|Le Bureau d’enregistrement a trouvé un{0}type non valide « » dans{2}la signature de la méthode « ». Utilisez'{1}'à la place.|
|XA4103|Le Bureau d’enregistrement a trouvé un{0}type non valide « » dans{2}la signature de la méthode « » : Le type implémente INativeObject, mais il n’a pas de constructeur qui accepte deux arguments (IntPtr, bool).|
|XA4104|Le Bureau d’enregistrement ne peut pas marshaler la{0}valeur de retour pour le type{1}' 'dans la signature de la méthode' '.|
|XA4105|Le Bureau d’enregistrement ne peut pas marshaler{0}le paramètre de type « »{1}dans la signature de la méthode « ».|
|XA4106|Le Bureau d’enregistrement ne peut pas marshaler la{0}valeur de retour pour la structure{1}' 'dans la signature de la méthode' '.|
|XA4107|Le Bureau d’enregistrement ne peut pas marshaler{0}le paramètre de type « »{1}dans la signature de la méthode « ».|
|XA4108|Le Bureau d’enregistrement ne peut pas obtenir le type ObjectiveC{0}pour le type managé «».|
|XA4109|Échec de la compilation du code d’Registrar généré. Veuillez signaler un [rapport de bogue](http://bugzilla.xamarin.com).|
|XA4110|Le Bureau d’enregistrement ne peut pas marshaler le{0}paramètre out de type' '{1}dans la signature de la méthode' '.|
|XA4111|Le Bureau d’enregistrement ne peut pas générer de{0}signature pour le type{1}' 'dans la méthode' '.|
|XA4200|Peut uniquement générer des ACW pour les types’CLAAS'.|
|XA4201|Impossible de déterminer le nom JNI pour {0}le type.|
|XA4203|Le nom de type spécifié doit être complet.|
|XA4204|Impossible de résoudre le type d'{0}interface' '. Vérifiez qu'il ne manque aucune référence d'assembly.|
|XA4205|[ExportField] ne peut être utilisé que sur les méthodes avec 0 paramètre.|
|XA4206|[Export] ne peut pas être utilisé sur un type générique.|
|XA4207|[ExportField] ne peut pas être utilisé sur un type générique.|
|XA4208|[Java. Interop. ExportFieldAttribute] ne peut pas être utilisé sur une méthode qui retourne void.|
|XA4209|Échec de la création de JavaTypeInfo pour {0} la classe {1}: en raison de.|
|XA4210|Vous devez ajouter une référence à mono. Android. Export. dll lorsque vous utilisez ExportAttribute ou ExportFieldAttribute.|
|XA4211|Fichier AndroidManifest. XML //uses-sdk/@android:targetSdkVersion '{0}'est inférieur à $ (TargetFrameworkVersion) '{1}'. Utilisation de l'{1} API-pour la compilation ACW.|

### <a name="xa5xxx-errors"></a>Erreurs XA5xxx

|Code d'erreur|Description|
|--- |--- |
|XA5101|Compilateur'{0}'manquant. Installez Android NDK.|
|XA5102|Échec de la conversion de l’assembly en code natif. Veuillez signaler un [rapport de bogue](http://bugzilla.xamarin.com).|
|XA5103|Échec de la compilation du fichier{0}' '. Veuillez signaler un [rapport de bogue](http://bugzilla.xamarin.com).|
|XA5201|Échec de la liaison native. Consultez les indicateurs d’utilisateur fournis à gcc :{0}|
|XA5202|Échec de la liaison native. Consultez le journal de génération.|
|XA5303|Avertissement de liaison native {0}:.|
|XA5300|Android SDK introuvable ou n’est pas entièrement installé.|
|XA5301|Outil « Strip » manquant. Installez le composant « outils en ligne de commande » Xcode.|
|XA5302|Outil’dsymutil’manquant. Installez le composant « outils en ligne de commande » Xcode.|
|XA5203|Échec de la génération des symboles de débogage (répertoire dSYM). Consultez le journal de génération.|
|XA5204|Impossible de supprimer le fichier binaire final. Consultez le journal de génération.|
|XA5205|Outil’AAPT’manquant. Installez le package d’outils de génération Android SDK.|
|XA5206|{0}. Le répertoire {1} de ressources Android n’existe pas.|
|XA5207|{0}. Le fichier {1} bibliothèque Java n’existe pas.|
|XA5208|Échec du téléchargement. Téléchargez {0} -le et placez-le {1} dans le répertoire.|
|XA5209|Échec de la décompression. Téléchargez {0} -le et extrayez {1} -le dans le répertoire.|
|XA5210|{0}. Le fichier {1} de bibliothèque Native n’existe pas.|

### <a name="xa6xxx-errors"></a>Erreurs XA6xxx

|Code d'erreur|Description|
|--- |--- |
|XA6001|L’exécution de la version de Cecil ne prend pas en charge la suppression d’assembly.|
|XA6002|Impossible de supprimer l’assembly'{0}'.|
|XA6003|Message UnauthorizedAccessException.|

### <a name="xa9xxx-errors"></a>Erreurs XA9xxx

Ces codes d’erreur sont les erreurs de licence et d’activation.

#### <a name="xa9000"></a>XA9000

 **Cause :** Licence expirée

 **Vérifié pendant :** Build

|Débutant|Indie|Professionnel (version d’évaluation)|Bureau|Entreprise|
|--- |--- |--- |--- |--- |
|WARNING|WARNING|WARNING|WARNING|WARNING|

#### <a name="xa9001"></a>XA9001

 **Cause :** Version d’évaluation expirée

 **Vérifié pendant :** Build

|Débutant|Indie|Professionnel (version d’évaluation)|Bureau|Entreprise|
|--- |--- |--- |--- |--- |
|WARNING|WARNING|WARNING|WARNING|WARNING|

#### <a name="xa9002"></a>XA9002

 **Cause :** AndroidJavaSource

 **Vérifié pendant :** Build

|Débutant|Indie|Professionnel (version d’évaluation)|Bureau|Entreprise|
|--- |--- |--- |--- |--- |
|ERROR|OK|OK|OK|OK|

 **Cause :** AndroidJavaLibrary

 **Vérifié pendant :** Build

|Débutant|Indie|Professionnel (version d’évaluation)|Bureau|Entreprise|
|--- |--- |--- |--- |--- |
|ERROR|OK|OK|OK|OK|

 **Si un assembly de liaison a le fichier. jar incorporé, il est intercepté au moment de l’exécution du package, et non pas au moment de la génération.**

 **Cause :** AndroidNativeLibrary

 **Vérifié pendant :** Build

|Débutant|Indie|Professionnel (version d’évaluation)|Bureau|Entreprise|
|--- |--- |--- |--- |--- |
|ERROR|OK|OK|OK|OK|

#### <a name="xa9003"></a>XA9003

 **Cause :** System.Runtime.Serialization

 **Vérifié pendant :** Package

|Débutant|Indie|Professionnel (version d’évaluation)|Bureau|Entreprise|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|OK|OK|OK|

 **Cause :** System.ServiceModel.Web

 **Vérifié pendant :** Package

|Débutant|Indie|Professionnel (version d’évaluation)|Bureau|Entreprise|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|OK|OK|OK|

 **Cause :** Mono.Data.Tds

 **Vérifié pendant :** Build

|Débutant|Indie|Professionnel (version d’évaluation)|Bureau|Entreprise|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|OK|OK|OK|

 **Elle est référencée par System. Data. dll, ce qui est autorisé**

 **Cause :** Mono.Android.Export

 **Vérifié pendant :** Build

|Débutant|Indie|Professionnel (version d’évaluation)|Bureau|Entreprise|
|--- |--- |--- |--- |--- |
|ERROR|OK|OK|OK|OK|

#### <a name="xa9004"></a>XA9004

 **Cause :** --profilage

 **Vérifié pendant :** Package

|Débutant|Indie|Professionnel (version d’évaluation)|Bureau|Entreprise|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|OK|OK|OK|

#### <a name="xa9005"></a>XA9005

 **Cause :** Limite de taille (32ko).

 **Vérifié pendant :** Package

|Débutant|Indie|Professionnel (version d’évaluation)|Bureau|Entreprise|
|--- |--- |--- |--- |--- |
|ERROR|OK|-|-|-|

#### <a name="xa9006"></a>XA9006

 **Cause :** Espace de noms System. Data. SqlClient.

 **Vérifié pendant :** Package

|Débutant|Indie|Professionnel (version d’évaluation)|Bureau|Entreprise|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|OK|OK|OK|

#### <a name="xa9008"></a>XA9008

 **Cause :** Génération à partir de la ligne de commande.

 **Vérifié pendant :** Build

|Débutant|Indie|Professionnel (version d’évaluation)|Bureau|Entreprise|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|OK|OK|OK|

#### <a name="xa9009"></a>XA9009

 **Cause :** Numéro de série manquant.

 **Vérifié pendant :** Ne pas être testé

|Débutant|Indie|Professionnel (version d’évaluation)|Bureau|Entreprise|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|ERROR|ERROR|ERROR|

#### <a name="xa9010"></a>XA9010

 **Cause :** ID de produit non valide.

 **Vérifié pendant :** Build

|Débutant|Indie|Professionnel (version d’évaluation)|Bureau|Entreprise|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|ERROR|ERROR|ERROR|

Équivalent à XA9018.

#### <a name="xa9011"></a>XA9011

 **Cause :** Échec de la mise à jour du fichier de licence (dans un nouveau format de fichier).

 **Vérifié pendant :** Activation

|Débutant|Indie|Professionnel (version d’évaluation)|Bureau|Entreprise|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|ERROR|ERROR|ERROR|

#### <a name="xa9012"></a>XA9012

 **Cause :** Aucun Internet

 **Vérifié pendant :** Activation

|Débutant|Indie|Professionnel (version d’évaluation)|Bureau|Entreprise|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|ERROR|ERROR|ERROR|

#### <a name="xa9013"></a>XA9013

 **Cause :** Erreur inconnue

 **Vérifié pendant :** Activation

|Débutant|Indie|Professionnel (version d’évaluation)|Bureau|Entreprise|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|ERROR|ERROR|ERROR|

#### <a name="xa9014"></a>XA9014

 **Cause :** Code d’activation non valide

 **Vérifié pendant :** Activation

|Débutant|Indie|Professionnel (version d’évaluation)|Bureau|Entreprise|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|ERROR|ERROR|ERROR|

#### <a name="xa9017"></a>XA9017

 **Cause :** Le serveur d’activation ne retourne pas une licence valide.

 **Vérifié pendant :** Activation

|Débutant|Indie|Professionnel (version d’évaluation)|Bureau|Entreprise|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|ERROR|ERROR|ERROR|

#### <a name="xa9018"></a>XA9018

**Cause :** Licence non valide

**Vérifié pendant :** Build

|Débutant|Indie|Professionnel (version d’évaluation)|Bureau|Entreprise|
|--- |--- |--- |--- |--- |
|-|-|ERROR|-|-|
