> Compétences illustrées : construire une application organisée en couches, développer la partie front-end d'une application, préparer et exécuter le déploiement d'une application


# AWS CDK - Introduction
Pour mettre en oeuvre ses infrastructures Cloud, AWS propose le service CloudFormation. Il permet de composer des fichiers de configuration d'infrastructures et d'automatiser leur déploiement.  
Le CDK génère ces fichiers de configuration en les programmant avec des classes et des outils de ligne de commandes pour automatiser tout ça. Le CDK est disponible dans de nombreux langages, agnostique sur le CLI, nous choisirons la version en Typescript pour notre découverte.

La partie découverte s'appuie sur [le tutoriel de démarrage d'AWS](https://aws.amazon.com/fr/getting-started/guides/setup-cdk/). Vous pouvez vous y référer si vous le souhaitez. Nous allons le reprendre, apporter quelques commentaires et debogages.
  
## Technologies
Nous aurons besoin de plusieurs technologies. Deux vont être nécessaires, AWS et le CDK pour manipuler les fichiers et les projets, d'autres pour mettre en oeuvre les technologies.  

### AWS : permet de gérer plein de trucs dont la connextion
Pour l'installation, vous pouvez essayer cette commande :
> __curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"__  

[Pour plus d'infos](https://docs.aws.amazon.com/fr_fr/cli/latest/userguide/getting-started-install.html#getting-started-install-instructions).
Vous pouvez tester l'installation avec la commande : _aws --version_, _aws_ étant la commande de base de la technologie.

### CDK : les commandes de conception
Les commandes du CDK permettent de créer un projet, le déployer ou le détruire. [Pour des infos générales...](https://aws.amazon.com/fr/getting-started/guides/setup-cdk/module-one/)  

Il s'installe comme ça :
> npm install -g aws-cdk

Une fois installé, il faut envoyer vos fichiers de configuration dans le Cloud pour permettre les syncrhonisations. Vous avez besoin de votre identifiant personnel, facile à trouver sur la console ou avec cette commande : _aws sts get-caller-identity_  
C'est parti pour le ['bootstrap'](https://docs.aws.amazon.com/cdk/api/v2/docs/aws-cdk-lib-readme.html) :
> __cdk bootstrap aws://VOTRE-NUMERO-DE-COMPTE/CODE-DE-LA-REGION__

exemple : __aws://123456789/eu-west-3__ (le code de Paris sur le Cloud Amazon)

Voilà, c'est tout.Dans la suite du tuto il y a des petites bouses pas très sympas. 

On peut créer une petite appli de démarrage [en suivant la troisième étape du tuto](https://aws.amazon.com/fr/getting-started/guides/setup-cdk/module-three/) avec quelques commentaires :  
#### Les profiles et l'identification
- vous pouvez ajouter un compte aws sur votre session. Il s'agit pour ça soit d'appliquer la commande __aws configure__ ou d'éditer ce fichier sur votre poste : __~/.aws/credentials__. L'édition du fichier permet d'ajouter plusieurs comptes :
> [default]
aws_access_key_id=VOTRE-CLE-ACCES
aws_secret_access_key=LE-SECRET-EN-LIEN-AVEC-LA-CLE
aws_default_region=CODE-REGION

[__Default__] est le compte par défaut, vous pouvez en ajouter d'autres en indiquant n'importe quel nom. On peut ensuite les appeler en ajoutant l'option __--profile NomDeProfil__

#### Considérations sur les versions
L'appel aux classes du CDK en utilisant le paquet par défaut _'aws-cdk-lib'_ pose des problèmes de versions si vous utilisez des librairies des services spécifiques installés par NPM (Dynamodb, lambda, EC2). Ce ne sont pas les mêmes versions. Si vous voulez utiliser la version ancienne (aws-cdk/core) pour utiliser NPM, il faut la remplacer dans vos codes par le ['core' du CDK](https://www.npmjs.com/package/@aws-cdk/core) comme suit :
D'abord on l'installe :  
> npm install --save @aws-cdk/core

Puis
> import * as cdk from 'aws-cdk-lib'

remplacé par
> import * as cdk from '@aws-cdk/core'  

Supprimez la ligne d'import du Construct. Utilisez ensuite celui du coeur du SDK : cdk.Construct dans le fichier du dossier lib. vous devriez avoir ce constructeur :
>   constructor(scope: cdk.Construct, id: string, props?: cdk.StackProps) 

Ca devrait rouler.  

Sinon, dans les tutoriels que vous trouverez, remplacez les appels aux librairies NPM par celles déjà présentent dans la _'aws-cdk-lib'_, exemple :
> import * as lambda from 'aws-cdk-lib/aws-lambda'
  
## Gérer la stack
Déployez la stack avec :
> cdk deploy
Détruisez la avec :
> cdk destroy  

Des commandes vous seront utiles pour connaître votre situation sur le déploiement de la stack en lien avec votre environnement :
> cdk doctor  

Pour regarder le fichier JSON pour CloudFormation. Bon, c'est abscons :
> cdk synth

[La liste des fonctions peut être trouvée ici](https://docs.aws.amazon.com/cdk/v2/guide/cli.html).