---
title: Utilisation de UrhoSharp pour créer un jeu 3D
description: Ce document fournit une vue d’ensemble de UrhoSharp, décrivant des scènes, des composants, des formes, des appareils photo, des actions, des entrées d’utilisateur, des sons et bien plus encore.
ms.prod: xamarin
ms.assetid: D9BEAD83-1D9E-41C3-AD4B-3D87E13674A0
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: f9ea9c4f6f2c920d903bd6f136dd0b98ddc7bf57
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84574325"
---
# <a name="using-urhosharp-to-build-a-3d-game"></a>Utilisation de UrhoSharp pour créer un jeu 3D

Avant d’écrire votre premier jeu, vous devez vous familiariser avec les principes de base : comment configurer votre scène, comment charger des ressources (contient votre illustration) et comment créer des interactions simples pour votre jeu.

<a name="scenenodescomponentsandcameras"></a>

## <a name="scenes-nodes-components-and-cameras"></a>Scènes, nœuds, composants et appareils photo

Le modèle de scène peut être décrit comme un graphique de scène basé sur des composants. La scène se compose d’une hiérarchie de nœuds de scène, à partir du nœud racine, qui représente également la scène entière. Chaque `Node` a une transformation 3D (position, rotation et échelle), un nom, un ID, ainsi qu’un nombre arbitraire de composants.  Les composants mettent un nœud à la vie, ils peuvent ajouter une représentation visuelle ( `StaticModel` ), ils peuvent émettre un son ( `SoundSource` ), ils peuvent fournir une limite de collision, et ainsi de suite.

Vous pouvez créer des scènes et des nœuds de configuration à l’aide de l' [éditeur Urho](#urhoeditor), ou vous pouvez effectuer des opérations à partir de votre code C#.  Dans ce document, nous allons explorer la configuration des éléments à l’aide de code, car ils illustrent les éléments nécessaires pour faire apparaître les choses sur votre écran

En plus de configurer votre scène, vous devez configurer un `Camera` , ce qui détermine ce qui sera affiché à l’utilisateur.

### <a name="setting-up-your-scene"></a>Configuration de votre scène

En général, vous créez ce formulaire dans votre méthode Start :

```csharp
var scene = new Scene ();
// Create the Octree component to the scene. This is required before
// adding any drawable components, or else nothing will show up. The
// default octree volume will be from -1000, -1000, -1000) to
//(1000, 1000, 1000) in world coordinates; it is also legal to place
// objects outside the volume but their visibility can then not be
// checked in a hierarchically optimizing manner
scene.CreateComponent<Octree> ();
// Create a child scene node (at world origin) and a StaticModel
// component into it. Set the StaticModel to show a simple plane mesh
// with a "stone" material. Note that naming the scene nodes is
// optional. Scale the scene node larger (100 x 100 world units)
var planeNode = scene.CreateChild("Plane");
planeNode.Scale = new Vector3 (100, 1, 100);
var planeObject = planeNode.CreateComponent<StaticModel> ();
planeObject.Model = ResourceCache.GetModel ("Models/Plane.mdl");
planeObject.SetMaterial(ResourceCache.GetMaterial("Materials/StoneTiled.xml"));
```

### <a name="components"></a>Components

Le rendu des objets 3D, la lecture audio, la physique et les mises à jour de logiques scriptées sont tous activés en créant des composants différents dans les nœuds en appelant `CreateComponent<T>()` .  Par exemple, configurez votre nœud et votre composant léger comme suit :

```csharp
// Create a directional light to the world so that we can see something. The
// light scene node's orientation controls the light direction; we will use
// the SetDirection() function which calculates the orientation from a forward
// direction vector.
// The light will use default settings (white light, no shadows)
var lightNode = scene.CreateChild("DirectionalLight");
lightNode.SetDirection (new Vector3(0.6f, -1.0f, 0.8f));
```

Nous avons créé au-dessus d’un nœud avec le nom « `DirectionalLight` » et définissons son sens, mais rien d’autre.  Maintenant, nous pouvons transformer le nœud ci-dessus en nœud à émission légère, en lui attachant un `Light` composant, avec `CreateComponent` :

```csharp
var light = lightNode.CreateComponent<Light>();
```

Les composants créés dans le `Scene` lui-même ont un rôle spécial : pour implémenter des fonctionnalités à l’intérieur de la scène. Ils doivent être créés avant tous les autres composants et inclure les éléments suivants :

 `Octree`: implémente le partitionnement spatial et les requêtes de visibilité accélérée. Sans cet objet 3D ne peut pas être rendu.
 `PhysicsWorld`: implémente la simulation physique. Les composants physiques tels que `RigidBody` ou `CollisionShape` ne peuvent pas fonctionner correctement sans ce.
 `DebugRenderer`: implémente le rendu de la géométrie de débogage.

Composants ordinaires comme `Light` , `Camera` ou`StaticModel`
ne doit pas être créé directement dans le `Scene` , mais plutôt dans les nœuds enfants.

La bibliothèque est fournie avec une grande variété de composants que vous pouvez associer à vos nœuds pour les mettre en vie : éléments visibles par l’utilisateur (modèles), sons, corps rigides, formes de collision, appareils photo, sources de lumière, émetteurs de particules et bien plus encore.

### <a name="shapes"></a>Formes

Par commodité, plusieurs formes sont disponibles sous forme de nœuds simples dans l’espace de noms Urho. Shapes.  Il s’agit des zones, des sphères, des cônes, des cylindres et des plans.

### <a name="camera-and-viewport"></a>Caméra et fenêtre d’affichage

Tout comme la lumière, les appareils photo sont des composants. vous devez donc joindre le composant à un nœud, comme suit :

```csharp
var CameraNode = scene.CreateChild ("camera");
camera = CameraNode.CreateComponent<Camera>();
CameraNode.Position = new Vector3 (0, 5, 0);
```

Avec cela, vous avez créé un appareil photo et vous avez placé l’appareil photo dans le monde 3D, l’étape suivante consiste à indiquer à `Application` qu’il s’agit de l’appareil photo que vous souhaitez utiliser. pour cela, utilisez le code suivant :

```csharp
Renderer.SetViewPort (0, new Viewport (Context, scene, camera, null))
```

Vous devriez maintenant être en mesure de voir les résultats de votre création.

### <a name="identification-and-scene-hierarchy"></a>Hiérarchie d’identification et de scène

Contrairement aux nœuds, les composants n’ont pas de noms ; les composants à l’intérieur du même nœud sont identifiés uniquement par leur type, et l’index dans la liste des composants du nœud, qui est renseigné dans l’ordre de création, par exemple, vous pouvez récupérer le `Light` composant à partir de l' `lightNode` objet ci-dessus comme suit :

```csharp
var myLight = lightNode.GetComponent<Light>();
```

Vous pouvez également obtenir une liste de tous les composants en extrayant la `Components` propriété qui retourne un `IList<Component>` que vous pouvez utiliser.

Une fois créés, les nœuds et les composants obtiennent des ID d’entier de scène-Global. Elles peuvent être interrogées à partir de la scène à l’aide des fonctions `GetNode(uint id)` et `GetComponent(uint id)` . C’est beaucoup plus rapide que d’exécuter des requêtes de nœud de scène basées sur un nom récursif.

Il n’existe aucun concept intégré d’une entité ou d’un objet de jeu ; au lieu de cela, il revient au programmeur de décider de la hiérarchie de nœuds et dans quels nœuds placer une logique de script. En règle générale, les objets en mouvement libre dans le monde 3D sont créés en tant qu’enfants du nœud racine. Les nœuds peuvent être créés avec ou sans nom à l’aide de `CreateChild` . L’unicité des noms de nœuds n’est pas appliquée.

Lorsqu’il existe une composition hiérarchique, il est recommandé (et, en fait, nécessaire, car les composants n’ont pas leurs propres transformations 3D) pour créer un nœud enfant.

Par exemple, si un caractère contient un objet dans sa main, l’objet doit avoir son propre nœud, qui serait apparenté à l’OS main du caractère (également `Node` ).  L’exception est la physique `CollisionShape` , qui peut être offsetted et pivoté individuellement par rapport au nœud.

Notez que `Scene` la transformation propre est ignorée intentionnellement comme une optimisation lors du calcul des transformations dérivées du monde des nœuds enfants. par conséquent, la modification n’a aucun effet et doit être laissée telle quelle (position à l’origine, pas de rotation, pas de mise à l’échelle).

`Scene`les nœuds peuvent être librement reapparentés. Dans, les composants de contraste appartiennent toujours au nœud auquel ils sont attachés et ne peuvent pas être déplacés entre les nœuds. Les nœuds et les composants fournissent une `Remove` fonction pour accomplir cette mise en œuvre sans passer par le parent. Une fois le nœud supprimé, aucune opération sur le nœud ou le composant en question n’est sécurisée après l’appel de cette fonction.

Il est également possible de créer un `Node` qui n’appartient pas à une scène. Cela est utile, par exemple, lorsqu’un appareil photo se déplace dans une scène qui peut être chargée ou enregistrée, car l’appareil photo n’est pas enregistré avec la scène réelle et n’est pas détruit lors du chargement de la scène. Toutefois, Notez que la création de composants Geometry, physique ou script sur un nœud non attaché, puis son déplacement ultérieur vers une scène entraînera un fonctionnement correct de ces composants.

### <a name="scene-updates"></a>Mises à jour de la scène

Une scène dont les mises à jour sont activées (par défaut) est automatiquement mise à jour à chaque itération de la boucle principale.  Le gestionnaire d’événements de l’application `SceneUpdate` est appelé sur celui-ci.

Les nœuds et les composants peuvent être exclus de la mise à jour de la scène en les désactivant, consultez `Enabled` .  Le comportement dépend du composant spécifique, mais par exemple la désactivation d’un composant qui peut être dessiné le rend invisible, tout en désactivant un composant de source sonore. Si un nœud est désactivé, tous ses composants sont considérés comme étant désactivés, quel que soit leur propre état d’activation/désactivation.

## <a name="adding-behavior-to-your-components"></a>Ajout d’un comportement à vos composants

La meilleure façon de structurer votre jeu est de créer votre propre composant qui encapsule un acteur ou un élément de votre jeu.  Cela rend la fonctionnalité autonome, à partir des ressources utilisées pour l’afficher, à son comportement.

La façon la plus simple d’ajouter un comportement à un composant consiste à utiliser des actions, qui sont des instructions que vous pouvez placer en file d’attente et combiner avec la programmation asynchrone en C#.  Ainsi, le comportement de votre composant est très élevé et il est plus simple de comprendre ce qui se passe.

Vous pouvez également contrôler exactement ce qui se passe à votre composant en mettant à jour les propriétés de votre composant sur chaque image (décrite dans la section comportement basé sur des trames).

### <a name="actions"></a>Actions

Vous pouvez ajouter des comportements à des nœuds très facilement à l’aide d’actions.  Les actions peuvent modifier diverses propriétés de nœud et les exécuter sur une période donnée, ou les répéter plusieurs fois avec une courbe d’animation donnée.

Par exemple, imaginez un nœud « Cloud » sur votre scène, vous pouvez le faire disparaître de la manière suivante :

```csharp
await cloud.RunActionsAsync (new FadeOut (duration: 3))
```

Les actions sont des objets immuables, qui vous permettent de réutiliser l’action pour la conduite de différents objets.

Un idiome courant consiste à créer une action qui effectue l’opération inverse :

```csharp
var gotoExit = new MoveTo (duration: 3, position: exitLocation);
var return = gotoExit.Reverse ();
```

Dans l’exemple suivant, l’objet est en fondu pour vous pendant une période de trois secondes.  Vous pouvez également exécuter une action après une autre, par exemple, vous pouvez commencer par déplacer le Cloud, puis le masquer :

```csharp
await cloud.RunActionsAsync (
    new MoveBy  (duration: 1.5f, position: new Vector3(0, 0, 15),
    new FadeOut (duration: 3));
```

Si vous souhaitez que les deux actions se déroulent en même temps, vous pouvez utiliser l’action Parallel et fournir toutes les actions que vous souhaitez effectuer en parallèle :

```csharp
  await cloud.RunActionsAsync (
    new Parallel (
      new MoveBy  (duration: 3, position: new Vector3(0, 0, 15),
      new FadeOut (duration: 3)));
```

Dans l’exemple ci-dessus, le Cloud se déplacera et disparaîtra en même temps.

Vous remarquerez qu’ils utilisent C# `await` , ce qui vous permet de réfléchir de manière linéaire au comportement que vous souhaitez obtenir.

### <a name="basic-actions"></a>Actions de base

Voici les actions prises en charge dans UrhoSharp :

- Déplacement des nœuds : `MoveTo` , `MoveBy` ,,,, `Place` `BezierTo` `BezierBy` `JumpTo` ,`JumpBy`
- Rotation des nœuds : `RotateTo` ,`RotateBy`
- Mise à l’échelle des nœuds : `ScaleTo` ,`ScaleBy`
- Nœuds de fondu : `FadeIn` , `FadeTo` ,, `FadeOut` `Hide` ,`Blink`
- Teinte : `TintTo` ,`TintBy`
- Instantanés : `Hide` , `Show` , `Place` , `RemoveSelf` ,`ToggleVisibility`
- Bouclage : `Repeat` , `RepeatForever` ,`ReverseTime`

Les autres fonctionnalités avancées incluent la combinaison des `Spawn` `Sequence` actions et.

### <a name="easing---controlling-the-speed-of-your-actions"></a>Accélération : contrôle de la vitesse de vos actions

L’accélération est un moyen qui dirige la manière dont l’animation se déroulera et peut rendre vos animations beaucoup plus agréables.  Par défaut, vos actions auront un comportement linéaire, par exemple une `MoveTo` action aurait un mouvement très robotique.  Vous pouvez encapsuler vos actions dans une action d’accélération pour modifier le comportement, par exemple, une action qui démarrerait lentement le mouvement, accélérer et atteindre lentement la fin ( `EasyInOut` ).

Pour ce faire, encapsulez une action existante dans une action d’accélération, par exemple :

```csharp
await cloud.RunActionAsync (
   new EaseInOut (
     new MoveTo (duration: 3, position: new Vector (0,0,15)), rate:1))
```

Il existe de nombreux modes d’accélération, le graphique suivant montre les différents types d’accélération et leur comportement sur la valeur de l’objet qu’ils contrôlent sur la période de temps, du début à la fin :

![Modes d’accélération](using-images/easing.png "Ce graphique montre les différents types d’accélération et leur comportement sur la valeur de l’objet qu’ils contrôlent sur la période de temps")

### <a name="using-actions-and-async-code"></a>Utilisation d’actions et du code asynchrone

Dans votre sous- `Component` classe, vous devez introduire une méthode Async qui prépare le comportement de votre composant et pilote ses fonctionnalités.
Ensuite, vous appelez cette méthode à l’aide du `await` mot clé C# d’une autre partie de votre programme, soit votre méthode, soit `Application.Start` en réponse à un point d’utilisateur ou de récit dans votre application.

Par exemple :

```csharp
class Robot : Component {
    public bool IsAlive;
    async void Launch ()
    {
        // Dress up our robot
        var cache = Application.ResourceCache;
        var model = node.CreateComponent<StaticModel>();
        model.Model = cache.GetModel ("robot.mdl"));
        model.SetMaterial (cache.GetMaterial ("robot.xml"));
        Node.SetScale (1);

        // Bring the robot into our scene
        await Node.RunActionsAsync(
            new MoveBy(duration: 0.6f, position: new Vector3(0, -2, 0)));
        // Make him move around to avoid the user fire
        MoveRandomly(minX: 1, maxX: 2, minY: -3, maxY: 3, duration: 1.5f);
        // And simultaneously have him shoot at the user
        StartShooting();
    }

    protected async void MoveRandomly (float minX, float maxX,
                                       float minY, float maxY,
                       float duration)
    {
        while (IsAlive){
            var moveAction = new MoveBy(duration,
                new Vector3(RandomHelper.NextRandom(minX, maxX),
                            RandomHelper.NextRandom(minY, maxY), 0));
            await Node.RunActionsAsync(moveAction, moveAction.Reverse());
        }
    }
    protected async void StartShooting()
    {
        while (IsAlive && Node.Components.Count > 0){
            foreach (var weapon in Node.Components.OfType<Weapon>()){
                await weapon.FireAsync(false);
                if (!IsAlive)
                    return;
            }
            await Node.RunActionsAsync(new DelayTime(0.1f));
        }
    }
}
```

Dans la `Launch` méthode ci-dessus, trois actions sont démarrées : l’automate est dans la scène, cette action modifie l’emplacement du nœud sur une période de 0,6 secondes.  Étant donné qu’il s’agit d’une option async, cela se produit en même temps que l’instruction suivante, qui est l’appel à `MoveRandomly` .  Cette méthode modifie la position du robot en parallèle à un emplacement aléatoire.  Pour ce faire, vous devez effectuer deux actions composées, le déplacement vers un nouvel emplacement, puis revenir à la position d’origine et répéter cette opération tant que l’automate reste actif.  Et pour rendre les choses plus intéressantes, le robot continuera à filmer simultanément.  La prise de tir ne démarrera que toutes les 0,1 secondes.

### <a name="frame-based-behavior-programming"></a>Programmation des comportements basés sur des frames

Si vous souhaitez contrôler le comportement de votre composant sur une base frame par image au lieu d’utiliser des actions, vous devez remplacer la `OnUpdate` méthode de votre `Component` sous-classe.  Cette méthode est appelée une fois par Frame, et est appelée uniquement si vous affectez la valeur true à la propriété ReceiveSceneUpdates.

L’exemple suivant montre comment créer un `Rotator` composant, qui est ensuite attaché à un nœud, ce qui entraîne la rotation du nœud :

```csharp
class Rotator : Component {
    public Rotator()
    {
        ReceiveSceneUpdates = true;
    }
    public Vector3 RotationSpeed { get; set; }
    protected override void OnUpdate(float timeStep)
    {
        Node.Rotate(new Quaternion(
            RotationSpeed.X  timeStep,
            RotationSpeed.Y  timeStep,
            RotationSpeed.Z  timeStep),
            TransformSpace.Local);
    }
}
```

C’est ainsi que vous attachez ce composant à un nœud :

```csharp
Node boxNode = new Node();
var rotator = new Rotator() { RotationSpeed = rotationSpeed };
boxNode.AddComponent (rotator);
```

### <a name="combining-styles"></a>Combinaison de styles

Vous pouvez utiliser le modèle asynchrone/basé sur des actions pour programmer une grande partie du comportement, ce qui est parfait pour le style de programmation de type « Fire-and-oublie », mais vous pouvez également ajuster le comportement de votre composant pour exécuter également du code de mise à jour sur chaque frame.

Par exemple, dans la démonstration SamplyGame, ce code est utilisé dans la `Enemy` classe encode le comportement de base utilise des actions, mais il garantit également que les composants pointent vers l’utilisateur en définissant la direction du nœud avec `Node.LookAt` :

```csharp
    protected override void OnUpdate(SceneUpdateEventArgs args)
    {
        Node.LookAt(
            new Vector3(0, -3, 0),
            new Vector3(0, 1, -1),
            TransformSpace.World);
        base.OnUpdate(args);
    }
```

## <a name="loading-and-saving-scenes"></a>Chargement et enregistrement des scènes

Les scènes peuvent être chargées et enregistrées au format XML. consultez les fonctions `LoadXml` et `SaveXML` . Quand une scène est chargée, tout le contenu existant dans celle-ci (nœuds et composants enfants) est supprimé en premier. Les nœuds et les composants qui sont marqués comme temporaires avec la `Temporary` propriété ne sont pas enregistrés. Le sérialiseur gère l’ensemble des composants et des propriétés intégrés, mais il n’est pas suffisamment intelligent pour gérer les propriétés personnalisées et les champs définis dans les sous-classes de composants. Toutefois, il fournit deux méthodes virtuelles :

 `OnSerialize`où vous pouvez inscrire des États personnalisés pour la sérialisation

 `OnDeserialized`où vous pouvez obtenir vos États personnalisés enregistrés.

En règle générale, un composant personnalisé ressemble à ce qui suit :

```csharp
class MyComponent : Component {
    // Constructor needed for deserialization
    public MyComponent(IntPtr handle) : base(handle) { }
    public MyComponent() { }
    // user defined properties (managed state):
    public Quaternion MyRotation { get; set; }
    public string MyName { get; set; }

    public override void OnSerialize(IComponentSerializer ser)
    {
        // register our properties with their names as keys using nameof()
        ser.Serialize(nameof(MyRotation), MyRotation);
        ser.Serialize(nameof(MyName), MyName);
    }

    public override void OnDeserialize(IComponentDeserializer des)
    {
        MyRotation = des.Deserialize<Quaternion>(nameof(MyRotation));
        MyName = des.Deserialize<string>(nameof(MyName));
    }
    // called when the component is attached to some node
    public override void OnAttachedToNode()
    {
        var node = this.Node;
    }
}
```

### <a name="object-prefabs"></a>Prefabs de l’objet

Le simple fait de charger ou d’enregistrer des scènes entières n’est pas suffisamment flexible pour les jeux où de nouveaux objets doivent être créés de manière dynamique. En revanche, la création d’objets complexes et la définition de leurs propriétés dans du code sont également fastidieuses. Pour cette raison, il est également possible d’enregistrer un nœud de scène qui inclura ses nœuds enfants, ses composants et ses attributs. Celles-ci peuvent être chargées plus tard en tant que groupe.  Un objet enregistré est souvent appelé Prefab. Pour ce faire, deux possibilités s'offrent à vous :

- Dans le code en appelant `Node.SaveXml` sur le nœud
- Dans l’éditeur, en sélectionnant le nœud dans la fenêtre hiérarchie et en choisissant « enregistrer le nœud sous » dans le menu « fichier ».
- À l’aide de la commande « node » dans `AssetImporter` , qui enregistre la hiérarchie des nœuds de scène et tous les modèles contenus dans l’élément multimédia d’entrée (par exemple, un fichier Collada)

Pour instancier le nœud enregistré dans une scène, appelez `InstantiateXml` . Le nœud sera créé en tant qu’enfant de la scène, mais peut être librement apparenté après cela. La position et la rotation pour le placement du nœud doivent être spécifiées. Le code suivant montre comment instancier un Prefab `Ninja.xm` à une scène avec la position et la rotation souhaitées :

```csharp
var prefabPath = Path.Combine (FileSystem.ProgramDir,"Data/Objects/Ninja.xml");
using (var file = new File(Context, prefabPath, FileMode.Read))
{
    scene.InstantiateXml(file, desiredPos, desiredRotation,
        CreateMode.Replicated);
}
```

## <a name="events"></a>Événements

UrhoObjects déclencher un certain nombre d’événements, ceux-ci sont exposés en tant qu’événements C# sur les différentes classes qui les génèrent.  Outre le modèle d’événement basé sur C#, il est également possible d’utiliser les `SubscribeToXXX` méthodes qui vous permettront de vous abonner et de conserver un jeton d’abonnement que vous pourrez utiliser ultérieurement pour vous désabonner.  La différence réside dans le fait que la première permet à un grand nombre d’appelants de s’abonner, tandis que le deuxième n’en autorise qu’un seul, mais permet à l’approche de style lambda plus attrayante d’être utilisée, tout en permettant la suppression facile de l’abonnement.  Ils s’excluent mutuellement.

Quand vous vous abonnez à un événement, vous devez fournir une méthode qui prend un argument avec les arguments d’événement appropriés.

Par exemple, voici comment vous vous abonnez à un événement de pointage de la souris :

```csharp
public void override Start ()
{
    UI.MouseButtonDown += HandleMouseButtonDown;
}

void HandleMouseButtonDown(MouseButtonDownEventArgs args)
{
    Console.WriteLine ("button pressed");
}
```

Avec le style lambda :

```csharp
public void override Start ()
{
    UI.MouseButtonDown += args => {
        Console.WriteLine ("button pressed");
    };
}
```

Parfois, vous souhaiterez cesser de recevoir des notifications pour l’événement. dans ce cas, enregistrez la valeur de retour de l’appel à la `SubscribeTo` méthode et appelez la méthode d’annulation d’abonnement sur celle-ci :

```csharp
Subscription mouseSub;

public void override Start ()
{
    mouseSub = UI.SubscribeToMouseButtonDown (args => {
    Console.WriteLine ("button pressed");
      mouseSub.Unsubscribe ();
    };
}
```

Le paramètre reçu par le gestionnaire d’événements est une classe d’arguments d’événement fortement typée qui sera spécifique à chaque événement et contient la charge utile d’événement.

## <a name="responding-to-user-input"></a>Réponse aux entrées d’utilisateur

Vous pouvez vous abonner à divers événements, tels que les séquences de touches, en vous abonnant à l’événement et en répondant à l’entrée en cours de remise :

```csharp
Start ()
{
    UI.KeyDown += HandleKeyDown;
}

void HandleKeyDown (KeyDownEventArgs arg)
{
     switch (arg.Key){
     case Key.Esc: Engine.Exit (); return;
}
```

Toutefois, dans de nombreux scénarios, vous souhaitez que les gestionnaires de mise à jour de scène vérifient l’état actuel des clés lorsqu’elles sont mises à jour, et mettent à jour votre code en conséquence.  Par exemple, les éléments suivants peuvent être utilisés pour mettre à jour l’emplacement de la caméra en fonction de l’entrée au clavier :

```csharp
protected override void OnUpdate(float timeStep)
{
    Input input = Input;
    // Movement speed as world units per second
    const float moveSpeed = 4.0f;
    // Read WASD keys and move the camera scene node to the
    // corresponding direction if they are pressed
    if (input.GetKeyDown(Key.W))
        CameraNode.Translate(Vector3.UnitY  moveSpeed  timeStep, TransformSpace.Local);
    if (input.GetKeyDown(Key.S))
        CameraNode.Translate(new Vector3(0.0f, -1.0f, 0.0f)  moveSpeed  timeStep, TransformSpace.Local);
    if (input.GetKeyDown(Key.A))
        CameraNode.Translate(new Vector3(-1.0f, 0.0f, 0.0f)  moveSpeed  timeStep, TransformSpace.Local);
    if (input.GetKeyDown(Key.D))
        CameraNode.Translate(Vector3.UnitX  moveSpeed  timeStep, TransformSpace.Local);
}
```

## <a name="resources-assets"></a>Ressources (ressources)

Les ressources incluent la plupart des éléments de UrhoSharp qui sont chargés à partir du stockage de masse pendant l’initialisation ou le Runtime :

- `Animation`-utilisé pour les animations squelettiques
- `Image`-représente les images stockées dans divers formats graphiques
- `Model`-Modèles 3D
- `Material`-matériaux utilisés pour le rendu des modèles.
- `ParticleEffect`- [décrit](https://urho3d.github.io/documentation/1.4/_particles.html) le fonctionnement d’un émetteur de particule, consultez «[particules](#particles)» ci-dessous.
- `Shader`-nuanceurs personnalisés
- `Sound`-sons à lire, voir «[son](#sound)» ci-dessous.
- `Technique`-techniques de rendu des matériaux
- `Texture2D`-texture 2D
- `Texture3D`-texture 3D
- `TextureCube`-Texture de cube
- `XmlFile`

Elles sont gérées et chargées par le sous `ResourceCache` -système (disponible en tant que `Application.ResourceCache` ).

Les ressources elles-mêmes sont identifiées par leurs chemins d’accès de fichiers, par rapport aux répertoires de ressources ou aux fichiers de package inscrits. Par défaut, le moteur enregistre les répertoires `Data` de ressources et `CoreData` , ou les packages `Data.pak` et `CoreData.pak` s’ils existent.

Si le chargement d’une ressource échoue, une erreur est consignée et une référence null est retournée.

L’exemple suivant montre une méthode classique pour extraire une ressource du cache de ressources.  Dans ce cas, il s’agit d’une texture pour un élément d’interface utilisateur, qui utilise la `ResourceCache` propriété de la `Application` classe.

```csharp
healthBar.SetTexture(ResourceCache.GetTexture2D("Textures/HealthBarBorder.png"));
```

Les ressources peuvent également être créées manuellement et stockées dans le cache des ressources comme si elles avaient été chargées à partir du disque.

Les budgets de mémoire peuvent être définis par type de ressource : si les ressources consomment plus de mémoire que ce qui est autorisé, les ressources les plus anciennes sont supprimées du cache si elles ne sont plus utilisées. Par défaut, les budgets de mémoire sont définis sur illimité.

### <a name="bringing-3d-models-and-images"></a>Importation de modèles et d’images 3D

Urho3D essaie d’utiliser autant que possible des formats de fichier existants et définit des formats de fichier personnalisés uniquement lorsque cela est absolument nécessaire, par exemple pour les modèles (. mdl) et pour les animations (. ani). Pour ces types de ressources, Urho fournit un convertisseur- [AssetImporter](https://urho3d.github.io/documentation/1.4/_tools.html) qui peut consommer de nombreux formats 3D populaires tels que FBX, DAE, 3DS et obj, etc.

Il existe également un complément pratique pour Blender [https://github.com/reattiva/Urho3D-Blender](https://github.com/reattiva/Urho3D-Blender) qui peut exporter vos ressources Blender dans le format approprié pour Urho3D.

### <a name="background-loading-of-resources"></a>Chargement en arrière-plan des ressources

Normalement, lors de la demande de ressources à l’aide de l’une des `ResourceCache` `Get` méthodes de, elles sont chargées immédiatement dans le thread principal, ce qui peut prendre plusieurs millisecondes pour toutes les étapes requises (charger le fichier à partir du disque, analyser les données, télécharger sur GPU si nécessaire) et peut donc entraîner des chutes de fréquence.

Si vous connaissez à l’avance les ressources dont vous avez besoin, vous pouvez les demander pour qu’elles soient chargées dans un thread d’arrière-plan en appelant `BackgroundLoadResource` . Vous pouvez vous abonner à l’événement chargement en arrière-plan de la ressource à l’aide de la `SubscribeToResourceBackgroundLoaded` méthode. Il déterminera si le chargement était en fait une réussite ou un échec. En fonction de la ressource, seule une partie du processus de chargement peut être déplacée vers un thread d’arrière-plan, par exemple l’étape de chargement du GPU de fin doit toujours avoir lieu dans le thread principal. Notez que si vous appelez l’une des méthodes de chargement des ressources pour une ressource qui est mise en file d’attente pour le chargement en arrière-plan, le thread principal est bloqué jusqu’à ce que son chargement soit terminé.

La fonctionnalité de chargement de scène asynchrone `LoadAsync` et `LoadAsyncXML` offre la possibilité de charger en arrière-plan les ressources avant de continuer à charger le contenu de la scène. Il peut également être utilisé pour charger uniquement les ressources sans modifier la scène, en spécifiant le `LoadMode.ResourcesOnly` . Cela permet de préparer un fichier de scène ou d’objet Prefab pour une instanciation rapide.

Enfin, la durée maximale (en millisecondes) passée à chaque trame sur les ressources chargées en arrière-plan de finition peut être configurée en définissant la `FinishBackgroundResourcesMs` propriété sur `ResourceCache` .

<a name="sound"></a>

## <a name="sound"></a>Son

Le son est une partie importante du jeu, et l’infrastructure UrhoSharp permet de lire des sons dans votre jeu.  Vous jouez des sons en joignant un`SoundSource`
à un, puis en `Node` lisant un fichier nommé à partir de vos ressources.

Voici comment procéder :

```csharp
var explosionNode = Scene.CreateChild();
var sound = explosionNode.CreateComponent<SoundSource>();
soundSource.Play(Application.ResourceCache.GetSound("Sounds/ding.wav"));
soundSource.Gain = 0.5f;
soundSource.AutoRemove = true;
```

<a name="particles"></a>

## <a name="particles"></a>Particules

Les particules offrent un moyen simple d’ajouter des effets simples et peu coûteux à votre application.  Vous pouvez consommer des particules stockées au format PEX à l’aide d’outils tels que [http://onebyonedesign.com/flash/particleeditor/](http://onebyonedesign.com/flash/particleeditor/) .

Les particules sont des composants qui peuvent être ajoutés à un nœud.  Vous devez appeler la méthode du nœud `CreateComponent<ParticleEmitter2D>` pour créer la particule, puis configurer la particule en affectant à la propriété Effects un effet 2D qui est chargé à partir du cache de ressources.

Par exemple, vous pouvez appeler cette méthode sur votre composant pour afficher des particules rendues sous la forme d’une explosion quand elle atteint :

```csharp
public async void Explode (Component target)
{
    // show a small explosion when the missile reaches an aircraft.
    var cache = Application.ResourceCache;
    var explosionNode = Scene.CreateChild();
    explosionNode.Position = target.Node.WorldPosition;
    explosionNode.SetScale(1f);
    var particle = explosionNode.CreateComponent<ParticleEmitter2D>();
    particle.Effect = cache.GetParticleEffect2D("explosion.pex");
    var scaleAction = new ScaleTo(0.5f, 0f);
    await explosionNode.RunActionsAsync(
        scaleAction, new DelayTime(0.5f));
    explosionNode.Remove();
}
```

Le code ci-dessus crée un nœud d’éclatement qui est attaché à votre composant actuel, à l’intérieur de ce nœud d’éclatement, nous créons un émetteur de particule 2D et le configurons en définissant la propriété Effect.  Nous exécutons deux actions, une qui met à l’échelle le nœud pour qu’elle soit plus petite et une autre qui la laisse à cette taille pendant 0,5 secondes.  Ensuite, nous supprimons l’explosion, ce qui supprime également l’effet de particule de l’écran.

La particule ci-dessus affiche ce qui suit lors de l’utilisation d’une texture de sphère :

![Particules avec une texture de sphère](using-images/image-1.png "La particule ci-dessus affiche ce qui suit lors de l’utilisation d’une texture de sphère")

Et c’est ce qu’il semble si vous utilisez une texture bloquante :

![Particules avec une texture de zone](using-images/image-2.png "Et c’est ce qu’il semble si vous utilisez une texture bloquante")

## <a name="multi-threading-support"></a>Prise en charge du Multi-Threading

UrhoSharp est une bibliothèque à thread unique.  Cela signifie que vous ne devez pas tenter d’appeler des méthodes dans UrhoSharp à partir d’un thread d’arrière-plan, ou vous risquez d’endommager l’état de l’application et de bloquer votre application.

Si vous souhaitez exécuter du code en arrière-plan, puis mettre à jour les composants Urho sur l’interface utilisateur principale, vous pouvez utiliser la`Application.InvokeOnMain(Action)`
.  En outre, vous pouvez utiliser C# await et les API de tâche .NET pour vous assurer que le code est exécuté sur le thread approprié.

## <a name="urhoeditor"></a>UrhoEditor

Vous pouvez télécharger l’éditeur Urho pour votre plateforme à partir du [site Web Urho](http://urho3d.github.io/), accéder à téléchargements et choisir la dernière version.

## <a name="copyrights"></a>Droits d’auteur

Cette documentation contient le contenu d’origine de Xamarin Inc, mais s’appuie largement sur la documentation Open source pour le projet Urho3D et contient des captures d’écran du projet Cocos2D.
