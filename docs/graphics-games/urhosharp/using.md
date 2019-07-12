---
title: Utilisation de UrhoSharp pour créer un jeu 3D
description: Ce document fournit une vue d’ensemble de UrhoSharp, décrivant les scènes, composants, des formes, caméras, actions, l’entrée d’utilisateur, son et bien plus encore.
ms.prod: xamarin
ms.assetid: D9BEAD83-1D9E-41C3-AD4B-3D87E13674A0
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: 7307f7dcfcc6b5e2576ce4b425879ae05c5a6056
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832672"
---
# <a name="using-urhosharp-to-build-a-3d-game"></a>Utilisation de UrhoSharp pour créer un jeu 3D

Avant d’écrire votre premier jeu, vous souhaitez Familiarisez-vous avec les principes de base : comment configurer votre scène, comment charger des ressources (il contient votre illustration) et comment créer des interactions simples pour votre jeu.

<a name="scenenodescomponentsandcameras"/>

## <a name="scenes-nodes-components-and-cameras"></a>Arrière-plan, les nœuds, les composants et les caméras de surveillance

Le modèle de scène peut être décrit comme un graphique de scène basé sur les composants. La scène se compose d’une hiérarchie de nœuds de la scène, en commençant à partir du nœud racine, qui représente également la scène entière. Chaque `Node` a une transformation 3D (de position, de rotation et de mise à l’échelle), un nom, un ID, ainsi qu’un nombre arbitraire de composants.  Composants de placer un nœud à la durée de vie, elles permettent d’ajouter une représentation visuelle (`StaticModel`), ils peuvent émettre des sons (`SoundSource`), ils peuvent fournir une limite de collision, et ainsi de suite.

Vous pouvez créer votre scènes et les nœuds de programme d’installation à l’aide de la [Urho éditeur](#urhoeditor), ou vous pouvez effectuer des opérations à partir de votre code c#.  Dans ce document, nous explorerons les choses paramètre à l’aide de code, comme ils illustrent les éléments nécessaires pour vous aider à apparaître sur votre écran

Outre la définition de votre scène, vous devez configurer un `Camera`, c’est ce qui détermine ce qui doit obtenir affiché à l’utilisateur.

### <a name="setting-up-your-scene"></a>Configuration de votre scène

Vous créez généralement ce formulaire votre méthode de démarrage :

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

### <a name="components"></a>Composants

Rendu d’objets 3D, lecture du son, physique et mises à jour de la logique de l’aide de scripts sont tous activés en créant des différents composants dans les nœuds en appelant `CreateComponent<T>()`.  Par exemple, configurer votre nœud et le composant clair comme suit :

```csharp
// Create a directional light to the world so that we can see something. The
// light scene node's orientation controls the light direction; we will use
// the SetDirection() function which calculates the orientation from a forward
// direction vector.
// The light will use default settings (white light, no shadows)
var lightNode = scene.CreateChild("DirectionalLight");
lightNode.SetDirection (new Vector3(0.6f, -1.0f, 0.8f));
```

Nous avons créé ci-dessus un nœud portant le nom «`DirectionalLight`» et définissez une direction, mais rien d’autre.  Maintenant, nous pouvons nous tourner le nœud ci-dessus dans un nœud émetteur de lumière, en attachant un `Light` composant, avec `CreateComponent`:

```csharp
var light = lightNode.CreateComponent<Light>();
```

Les composants créés dans le `Scene` lui-même jouent un rôle particulier : pour implémenter les fonctionnalités à l’échelle de la scène. Ils doivent être créés avant tous les autres composants et incluent les éléments suivants :

 `Octree`: implémente le partitionnement spatial et accélération des requêtes de visibilité. Sans cette 3D les objets ne peuvent pas être affichés.
 `PhysicsWorld`: implémente simulation physique. Physique des composants tels que `RigidBody` ou `CollisionShape` ne peut pas fonctionner correctement sans cela.
 `DebugRenderer`: implémente déboguer le rendu de la géométrie.

Composants ordinaires, comme `Light`, `Camera` ou `StaticModel`
ne doivent pas être créées directement dans le `Scene`, mais plutôt dans les nœuds enfants.

La bibliothèque est fourni avec une grande variété de composants que vous pouvez attacher à des nœuds à leur donner vie : éléments visible par l’utilisateur (modèles), sons, corps rigides, les formes de collision, caméras, sources de lumière, émetteurs de particules et bien plus encore.

### <a name="shapes"></a>Formes

Pour des raisons pratiques, des formes différentes sont disponibles en tant que nœuds simples dans l’espace de noms Urho.Shapes.  Ceux-ci incluent des zones, des sphères, cônes, cylindres et plans.

### <a name="camera-and-viewport"></a>Appareil photo et la fenêtre d’affichage

Tout comme la lumière, les caméras sont des composants, donc vous devez attacher le composant à un nœud, comme suit :

```csharp
var CameraNode = scene.CreateChild ("camera");
camera = CameraNode.CreateComponent<Camera>();
CameraNode.Position = new Vector3 (0, 5, 0);
```

Avec cela, vous avez créé un appareil photo et vous avez placé la caméra dans le monde en 3D, l’étape suivante consiste à informer le `Application` qu’il s’agit de l’appareil photo que vous souhaitez utiliser, cela est effectué avec le code suivant :

```csharp
Renderer.SetViewPort (0, new Viewport (Context, scene, camera, null))
```

Et maintenant vous devez être en mesure de voir les résultats de votre création.

### <a name="identification-and-scene-hierarchy"></a>Hiérarchie d’identification et de la scène

Contrairement aux nœuds, les composants n’ont pas de noms ; composants à l’intérieur du même nœud sont uniquement identifiées par leur type et les index dans la liste des composants du nœud, qui est remplie dans l’ordre de création, par exemple, vous pouvez récupérer le `Light` composant hors du `lightNode` au-dessus de l’objet comme suit :

```csharp
var myLight = lightNode.GetComponent<Light>();
```

Vous pouvez également obtenir une liste de tous les composants en récupérant le `Components` propriété qui retourne un `IList<Component>` que vous pouvez utiliser.

Lors de la création, à la fois des nœuds et des composants obtenir les ID d’entier globale de scène. Elles peuvent être interrogées à partir de la scène en utilisant les fonctions `GetNode(uint id)` et `GetComponent(uint id)`. Cela est beaucoup plus rapide par exemple récursif des requêtes de nœud de scène en fonction du nom.

Il n’existe aucun concept intégrée d’une entité ou un objet de jeu ; au lieu de cela, il est sur le programmeur décider de la hiérarchie de nœuds et dans les nœuds à placer la logique de l’aide de scripts. En règle générale, libre de déplacer les objets dans le monde 3D doivent être créés en tant qu’enfants du nœud racine. Nœuds peuvent être créés avec ou sans un nom à l’aide `CreateChild`. L’unicité des noms de nœud n’est pas appliquée.

Chaque fois qu’il existe certaines composition hiérarchique, il est recommandé (et en fait nécessaire, étant donné que les composants n’ont pas leurs propres transformations 3D) pour créer un nœud enfant.

Par exemple si un caractère a été maintenant un objet dans la main, l’objet doit avoir son propre nœud, ce qui est apparenté à d’OS du caractère disponible (également un `Node`).  L’exception est la physique `CollisionShape`, qui peut être offsetted et pivotés individuellement en relation avec le nœud.

Notez que `Scene`du propriétaire de transformation est volontairement ignorée en guise d’optimisation lors du calcul des transformations de monde dérivée de nœuds enfants, ainsi, la modification n’a aucun effet et il doit être conservé comme il s’agit (position à l’origine, aucune rotation, aucune mise à l’échelle).

`Scene` nœuds peuvent être librement à nouveau apparentés. En revanche les composants appartiennent toujours au nœud attaché à, ils ne peuvent pas être déplacés entre les nœuds. Les nœuds et composants fournissent une `Remove` (fonction) pour y parvenir sans avoir à passer par le parent. Une fois que le nœud est supprimé, aucune opération sur le nœud ou d’un composant en question n’est sécurisées après l’appel de cette fonction.

Il est également possible de créer un `Node` qui n’appartient pas à une scène. Cela est utile, par exemple, avec une caméra déplacement dans une scène qui peut être chargée ou enregistrée, car l’appareil photo n’est pas enregistrée, ainsi que la scène réelle et puis ne sera pas détruit lors du chargement de la scène. Toutefois, notez que création de composants de géométrie, physique ou un script à un nœud non attaché, puis déplacement dans une scène entraînera ces composants ne fonctionne ne pas correctement.

### <a name="scene-updates"></a>Mises à jour de la scène

Une scène dont les mises à jour sont activées (valeur par défaut) est automatiquement actualisé sur chaque itération de boucle principale.  L’application `SceneUpdate` Gestionnaire d’événements est appelé dessus.

Les composants et les nœuds peuvent être exclus de la mise à jour de la scène en leur désactivation, consultez `Enabled`.  Le comportement varie selon le composant spécifique, mais par exemple la désactivation un composant drawable également rend invisible, tandis que la désactivation d’un composant source audio désactive le volume. Si un nœud est désactivé, tous ses composants sont traités comme étant désactivés quel que soit leur propre état Activer/désactiver.

## <a name="adding-behavior-to-your-components"></a>Ajouter un comportement à vos composants

La meilleure façon de structurer votre jeu consiste à créer votre propre composant qui encapsulent un acteur ou un élément sur votre jeu.  Cela rend la fonctionnalité autonome, des ressources utilisées pour l’afficher, son comportement.

Ajouter un comportement à un composant, le plus simple consiste à utiliser des actions, qui sont des instructions que vous pouvez en file d’attente et combiner ces informations avec la programmation async c#.  Cela autorise le comportement de votre composant puisse être très haut niveau et le rend plus simple à comprendre ce qui se passe.

Ou bien, vous pouvez contrôler ce qui se passe exactement pour votre composant en mettant à jour les propriétés de votre composant sur chaque trame (présenté dans la section de comportement en fonction du Frame).

### <a name="actions"></a>Actions

Vous pouvez ajouter le comportement aux nœuds très facilement à l’aide d’Actions.  Actions peuvent modifier différentes propriétés de nœud et les exécuter sur une période de temps ou les répéter un nombre de fois avec une courbe d’animation donnée.

Par exemple, considérez un nœud de « cloud » sur votre scène, vous pouvez le fondu comme suit :

```csharp
await cloud.RunActionsAsync (new FadeOut (duration: 3))
```

Les actions sont des objets immuables, ce qui vous permet de réutiliser l’action de piloter différents objets.

Un idiome courant consiste à créer une action qui effectue l’opération inverse :

```csharp
var gotoExit = new MoveTo (duration: 3, position: exitLocation);
var return = gotoExit.Reverse ();
```

L’exemple suivant serait fondu de l’objet pour vous sur une période de trois secondes.  Vous pouvez également exécuter une action après l’autre, par exemple, vous pouvez tout d’abord déplacer le cloud et ensuite de le masquer :

```csharp
await cloud.RunActionsAsync (
    new MoveBy  (duration: 1.5f, position: new Vector3(0, 0, 15),
    new FadeOut (duration: 3));
```

Si vous souhaitez que les deux actions aient lieu en même temps, vous pouvez utiliser l’action parallèle et fournir toutes les actions souhaitées effectuées en parallèle :

```csharp
  await cloud.RunActionsAsync (
    new Parallel (
      new MoveBy  (duration: 3, position: new Vector3(0, 0, 15),
      new FadeOut (duration: 3)));
```

Dans l’exemple ci-dessus, le nuage déplace et fondu en même temps.

Vous remarquerez à l’aide de ces C# `await`, ce qui permet de vous faire penser que de manière linéaire sur le comportement que vous souhaitez atteindre.

### <a name="basic-actions"></a>Actions de base

Voici les actions prises en charge dans UrhoSharp :

- Déplacement de nœuds : `MoveTo`, `MoveBy`, `Place`, `BezierTo`, `BezierBy`, `JumpTo`, `JumpBy`
- Rotation des nœuds : `RotateTo`, `RotateBy`
- Mise à l’échelle des nœuds : `ScaleTo`, `ScaleBy`
- Nœuds de fondu : `FadeIn`, `FadeTo`, `FadeOut`, `Hide`, `Blink`
- Teintes : `TintTo`, `TintBy`
- Instant : `Hide`, `Show`, `Place`, `RemoveSelf`, `ToggleVisibility`
- Bouclage : `Repeat`, `RepeatForever`, `ReverseTime`

Autres fonctionnalités avancées incluent la combinaison de la `Spawn` et `Sequence` actions.

### <a name="easing---controlling-the-speed-of-your-actions"></a>Atténuation : contrôle la vitesse de vos actions

L’accélération est une façon qui dirige la façon dont l’animation sera dérouler, et elle peut rendre vos animations beaucoup plus agréable.  Par défaut vos actions ont un comportement linéaire, par exemple un `MoveTo` action aurait un mouvement très robotisé.  Vous pouvez encapsuler vos Actions dans une action d’accélération pour modifier le comportement, par exemple, celui qui serait lentement démarrer le déplacement, accélérez et lentement à la fin (`EasyInOut`).

Pour cela, en encapsulant une Action existante dans une action d’accélération, par exemple :

```csharp
await cloud.RunActionAsync (
   new EaseInOut (
     new MoveTo (duration: 3, position: new Vector (0,0,15)), rate:1))
```

Il existe plusieurs modes d’accélération, le graphique suivant montre les différents types d’accélération et de leur comportement sur la valeur de l’objet qu’ils sont contrôler la période de temps, à partir du début à la fin :

![Modes d’accélération](using-images/easing.png "ce graphique montre les différents types d’accélération et de leur comportement sur la valeur de l’objet qu’ils sont le contrôle sur la période de temps")

### <a name="using-actions-and-async-code"></a>À l’aide de code des actions et async

Dans votre `Component` sous-classe, vous devez introduire une méthode async qui prépare votre comportement des composants et les fonctionnalités des disques pour elle.
Puis vous devez appeler cette méthode à l’aide de C# `await` mot clé à partir d’une autre partie de votre programme, soit votre `Application.Start` méthode ou en réponse à un point d’utilisateur ou d’un article dans votre application.

Exemple :

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

Dans la `Launch` méthode mentionnée ci-dessus trois actions sont démarrés : le robot est fourni dans la scène, cette action modifie l’emplacement du nœud sur une période de 0,6 seconde.  Dans la mesure où il s’agit d’une option async, cela se produit simultanément en tant que l’instruction suivante qui est l’appel à `MoveRandomly`.  Cette méthode modifie la position du robot en parallèle vers un emplacement aléatoire.  Pour ce faire, vous devez effectuer deux actions composées, le déplacement vers un nouvel emplacement et en revenant à la version d’origine de positionner et répétez cette opération tant que le robot reste actif.  Et pour rendre les choses plus intéressantes, le robot sera images simultanément.  Le tir ne démarrera que toutes les secondes 0,1.

### <a name="frame-based-behavior-programming"></a>Programmation du comportement en fonction du frame

Si vous souhaitez contrôler le comportement de votre composant sur une base d’image par image au lieu d’utiliser des actions, ce que vous feriez consiste à substituer la `OnUpdate` méthode de votre `Component` sous-classe.  Cette méthode est appelée une fois par frame et est appelée uniquement si vous définissez la propriété ReceiveSceneUpdates sur true.

L’exemple suivant montre comment créer un `Rotator` composant, qui est ensuite attaché à un nœud, ce qui provoque le nœud effectuer la rotation :

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

Et Voici comment vous devez attacher ce composant à un nœud :

```csharp
Node boxNode = new Node();
var rotator = new Rotator() { RotationSpeed = rotationSpeed };
boxNode.AddComponent (rotator);
```

### <a name="combining-styles"></a>Combinaison de styles

Vous pouvez utiliser le modèle async/en fonction des actions pour la programmation d’une grande partie du comportement qui est idéal pour déclencher et oublier le style de programmation, mais vous pouvez également ajuster comportement de votre composant pour également exécuter du code de mise à jour sur chaque image.

Par exemple, dans la démonstration SamplyGame cela est utilisé dans le `Enemy` classe encode les actions d’utilisations de comportement de base, mais il garantit également que les composants pointent vers l’utilisateur en définissant la direction du nœud avec `Node.LookAt`:

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

## <a name="loading-and-saving-scenes"></a>Chargement et d’enregistrement des scènes

Arrière-plan peut être chargés et enregistrés au format XML ; consultez les fonctions `LoadXml` et `SaveXML`. Quand une scène est chargée, tous les contenus existants qu’il contient (nœuds enfants et composants) sont tout d’abord supprimé. Nœuds et des composants qui sont marqués temporaires avec le `Temporary` propriété n’est pas enregistrée. Le sérialiseur gère toutes les propriétés et les composants intégrés, mais il n’est pas suffisamment intelligent pour gérer les propriétés personnalisées et les champs définis dans les sous-classes de votre composant. Toutefois, il fournit deux méthodes virtuelles pour cela :

 `OnSerialize` où vous pouvez vous inscrire des états personnalisés pour la sérialisation

 `OnDeserialized` où vous pouvez obtenir vos états personnalisés enregistrés.

En règle générale, un composant personnalisé se présente comme suit :

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

### <a name="object-prefabs"></a>Objet prefabs

Simplement de chargement ou de l’enregistrement des scènes entières n’est pas suffisamment flexible pour les jeux où nouveaux objets doivent être créées dynamiquement. En revanche, la création d’objets complexes et en définissant leurs propriétés dans le code sera également fastidieux. Pour cette raison, il est également possible d’enregistrer un nœud de scène qui inclut ses nœuds enfants, les composants et les attributs. Il peuvent plus tard être chargées en tant que groupe.  Ce type d’un objet enregistré est souvent appelé un préfabriqué. Il y a trois manières pour effectuer cette opération :

- Dans le code en appelant `Node.SaveXml` sur le nœud
- Dans l’éditeur, en sélectionnant le nœud dans la fenêtre de hiérarchie et en choisissant « Enregistrer nœud en tant que » dans le menu « Fichier ».
- À l’aide de la commande « nœud » dans `AssetImporter`, qui enregistrera la hiérarchie de nœud de scène et tous les modèles contiennent dans l’élément multimédia d’entrée (par exemple). un fichier Collada)

Pour instancier le nœud enregistré dans une scène, appelez `InstantiateXml`. Le nœud sera créé en tant qu’enfant de la scène, mais peut être librement à nouveau apparenté après cela. Position et rotation pour placer le nœud doivent être spécifiés. Le code suivant montre comment instancier un préfabriqué `Ninja.xm` à une scène avec la rotation et de position souhaitée :

```csharp
var prefabPath = Path.Combine (FileSystem.ProgramDir,"Data/Objects/Ninja.xml");
using (var file = new File(Context, prefabPath, FileMode.Read))
{
    scene.InstantiateXml(file, desiredPos, desiredRotation,
        CreateMode.Replicated);
}
```

## <a name="events"></a>Événements

UrhoObjects générer un nombre d’événements, ceux-ci sont exposés en tant que les événements c# sur les différentes classes qui génèrent les.  En plus de celle de C#-basée sur modèle d’événement, il est également possible d’utiliser une la `SubscribeToXXX` méthodes qui vous permettra de s’abonner et conserver un jeton d’abonnement que vous pouvez utiliser ultérieurement pour annuler l’abonnement.  La différence est que le premier autoriser les appelants de nombreuses pour s’abonner, tandis que la deuxième identité uniquement permet une, mais le lambda-style mieux approche à utiliser et autorise pas encore, de suppression de l’abonnement.  Ils s’excluent mutuellement.

Lorsque vous vous abonnez à un événement, vous devez fournir une méthode qui accepte un argument avec les arguments d’événement approprié.

Il s’agit par exemple, comment vous abonnez à un bouton de souris événement d’arrêt :

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

Avec le style de l’expression lambda :

```csharp
public void override Start ()
{
    UI.MouseButtonDown += args => {
        Console.WriteLine ("button pressed");
    };
}
```

Parfois, vous souhaitez cesser de recevoir des notifications pour l’événement, dans ce cas, enregistrez la valeur de retour de l’appel à `SubscribeTo` (méthode) et appeler la méthode de résiliation d’abonnement dessus :

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

Le paramètre reçu par le Gestionnaire d’événements est une classe d’arguments d’événement fortement typé qui sera spécifique à chaque événement et qui contient la charge utile d’événement.

## <a name="responding-to-user-input"></a>Répondre aux entrées utilisateur

Vous pouvez vous abonner à différents événements, tels que des séquences de touches vers le bas, en vous abonnant à l’événement et répond à l’entrée de remise :

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

Mais dans de nombreux scénarios, vous souhaitez que vos gestionnaires de mise à jour de scène pour vérifier l’état actuel des touches lorsqu’ils sont mis à jour et mettre à jour votre code en conséquence.  Par exemple, ce qui suit peut être utilisé pour mettre à jour l’emplacement de l’appareil photo en fonction de l’entrée au clavier :

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

## <a name="resources-assets"></a>Ressources (actifs)

Les ressources incluent la plupart des choses dans UrhoSharp qui sont chargés à partir de stockage de masse pendant l’initialisation ou de runtime :

- `Animation` -utilisé pour les animations squelettes
- `Image` -représente les images stockées dans un large éventail de formats graphiques
- `Model` -Modèles 3D
- `Material` -permet de restituer les modèles de documents.
- `ParticleEffect`- [Décrit](http://urho3d.github.io/documentation/1.4/_particles.html) fonctionnement d’un émetteur de particules, consultez «[particules](#particles)» ci-dessous.
- `Shader` -des nuanceurs personnalisés
- `Sound` -sons pour la lecture, consultez «[son](#sound)» ci-dessous.
- `Technique` -techniques de rendu matérielles
- `Texture2D` -Texture 2D
- `Texture3D` -Texture 3D
- `TextureCube` -Texture de cube
- `XmlFile`

Ils sont gérés et chargés par le `ResourceCache` sous-système (disponible en tant que `Application.ResourceCache`).

Les ressources elles-mêmes sont identifiés par leurs chemins d’accès de fichier, par rapport à des répertoires de ressources inscrit ou des fichiers de package. Par défaut, le moteur enregistre les répertoires de ressources `Data` et `CoreData`, ou les packages `Data.pak` et `CoreData.pak` s’ils existent.

Si le chargement d’une ressource échoue, une erreur est consignée et une référence null est retournée.

L’exemple suivant montre une manière classique de l’extraction d’une ressource à partir du cache de ressources.  Dans ce cas, une texture pour un élément d’interface utilisateur, cet exemple utilise le `ResourceCache` propriété à partir de la `Application` classe.

```csharp
healthBar.SetTexture(ResourceCache.GetTexture2D("Textures/HealthBarBorder.png"));
```

Ressources peuvent également être créés manuellement et stockées dans le cache de ressources comme s’ils avaient été chargés à partir du disque.

Les budgets de mémoire peuvent être définies par le type de ressource : si ressources consomment plus de mémoire autorisée, les ressources plus ancien seront supprimés à partir du cache si rien n’est en cours d’utilisation plus. Par défaut les budgets de mémoire sont définies sur un nombre illimités.

### <a name="bringing-3d-models-and-images"></a>En associant les modèles 3D et des images

Urho3D tente d’utiliser des formats de fichiers existants si possible et définir des formats de fichiers personnalisés uniquement lorsque cela est absolument nécessaire comme pour les modèles (.mdl) et des animations (.ani). Pour ces types de ressources, Urho fournit un convertisseur de - [AssetImporter](http://urho3d.github.io/documentation/1.4/_tools.html) qui peut consommer de nombreux formats 3D populaires tels que fbx, dae, 3ds et obj, etc.

Il existe également un complément utile pour Blender [ https://github.com/reattiva/Urho3D-Blender ](https://github.com/reattiva/Urho3D-Blender) qui peut exporter vos ressources Blender dans le format qui convient pour Urho3D.

### <a name="background-loading-of-resources"></a>Chargement en arrière-plan des ressources

Normalement, lors de la demande de ressources de la `ResourceCache`de `Get` (méthode), ils sont chargés immédiatement dans le thread principal, ce qui peut prendre plusieurs millisecondes pour toutes les étapes requises (charger le fichier à partir du disque, analyser les données, charger vers GPU si nécessaire ) et peuvent donc entraîner gouttes de fréquence d’images.

Si vous connaissez à l’avance les ressources auxquelles vous avez besoin, vous pouvez demander à être chargés dans un thread d’arrière-plan en appelant `BackgroundLoadResource`. Vous pouvez vous abonner à l’événement de chargement de la ressource d’arrière-plan à l’aide de la `SubscribeToResourceBackgroundLoaded` (méthode). Il vous indique si le chargement a été réellement une réussite ou échec. Selon la ressource, uniquement une partie du processus de chargement peut être déplacé vers un thread d’arrière-plan, par exemple l’étape de chargement GPU finition doit toujours se produire dans le thread principal. Notez que si vous appelez une de la ressource du chargement des méthodes pour une ressource qui est en file d’attente pour le chargement d’arrière-plan, le thread principal va se bloquer jusqu'à ce que son chargement est terminé.

La fonctionnalité de chargement de scène asynchrone `LoadAsync` et `LoadAsyncXML` a la possibilité de charge en arrière-plan les ressources avant de pouvoir passer pour charger le contenu de la scène. Il peut également être utilisé pour charger uniquement les ressources sans modifier la scène, en spécifiant le `LoadMode.ResourcesOnly`. Cela permet de préparer un fichier prefab scène ou l’objet pour l’instanciation rapide.

Pour finir la durée maximale (en millisecondes) consacré à chaque frame finition d’arrière-plan chargé des ressources peuvent être configurées en définissant le `FinishBackgroundResourcesMs` propriété sur le `ResourceCache`.

<a name="sound"/>

## <a name="sound"></a>Signal sonore

Son est une partie importante de jeu, et l’infrastructure de UrhoSharp offre un moyen de lire des sons dans votre jeu.  Lire les sons en attachant un `SoundSource`
composant à un `Node` et puis lire un fichier nommé à partir de vos ressources.

Voici comment procéder :

```csharp
var explosionNode = Scene.CreateChild();
var sound = explosionNode.CreateComponent<SoundSource>();
soundSource.Play(Application.ResourceCache.GetSound("Sounds/ding.wav"));
soundSource.Gain = 0.5f;
soundSource.AutoRemove = true;
```

<a name="particles"/>

## <a name="particles"></a>Particules

PARTICULES offrent un moyen simple d’ajouter quelques effets simples et peu onéreux à votre application.  Vous pouvez consommer des particules stockées au format PEX, à l’aide des outils tels que [ http://onebyonedesign.com/flash/particleeditor/ ](http://onebyonedesign.com/flash/particleeditor/).

Particules sont des composants qui peuvent être ajoutés à un nœud.  Vous devez appeler le nœud `CreateComponent<ParticleEmitter2D>` méthode pour créer la particule, puis configurer la particule en définissant la propriété Effect à un effet 2D qui est chargée à partir du cache de ressources.

Par exemple, vous pouvez appeler cette méthode dans votre composant pour afficher des particules qui sont rendus sous la forme d’une explosion lorsqu’il atteint :

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

Le code ci-dessus crée un nœud d’explosion qui est attaché à votre composant en cours, à l’intérieur de ce nœud explosion nous créer un émetteur de particules 2D et configurez-le en définissant la propriété Effect.  Nous exécutons les deux actions, un qui s’adapte le nœud pour être plus petits et un qui laisse à cette taille de 0,5 secondes.  Ensuite, nous supprimons l’explosion, ce qui supprime également l’effet de particules à partir de l’écran.

La particule ci-dessus s’affiche comme suit lors de l’utilisation d’une texture de la sphère :

![PARTICULES avec une texture de la sphère](using-images/image-1.png "la particule ci-dessus affiche sous cette forme lors de l’utilisation d’une texture de la sphère")

Et c’est ce que cela donne si vous utilisez une texture sous forme de blocs :

![PARTICULES avec une texture de la zone](using-images/image-2.png "et c’est ce que cela donne si à l’aide d’une texture sous forme de blocs")

## <a name="multi-threading-support"></a>Prise en charge multi-threading

UrhoSharp est une bibliothèque de thread unique.  Cela signifie que vous ne devriez pas appeler des méthodes dans UrhoSharp à partir d’un thread d’arrière-plan ou risque d’endommager l’état de l’application et probablement se bloquer votre application.

Si vous souhaitez exécuter du code en arrière-plan et mettez ensuite à jour les composants Urho sur l’interface utilisateur principale, vous pouvez utiliser la `Application.InvokeOnMain(Action)`
.  En outre, vous pouvez utiliser await de c# et .NET tâches API pour vous assurer que le code est exécuté sur le thread approprié.

## <a name="urhoeditor"></a>UrhoEditor

Vous pouvez télécharger l’éditeur Urho pour votre plateforme depuis le [site Web Urho](http://urho3d.github.io/), accédez aux téléchargements et choisir la version la plus récente.

## <a name="copyrights"></a>Copyrights

Cette documentation contient le contenu d’origine à partir de Xamarin Inc, mais il dessine largement à partir de la documentation open source pour le projet Urho3D et contient des captures d’écran à partir du projet Cocos2D.
