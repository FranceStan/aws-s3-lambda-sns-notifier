
# 📡 Système de Notification d’Événements AWS (S3 → Lambda → SNS)

Ce projet illustre la mise en place d’un système de notification automatique à partir d’un événement dans Amazon S3. Il a été réalisé dans le cadre de ma préparation à la certification **AWS Solutions Architect Associate**.

## 🎯 Objectif
Recevoir un email automatique chaque fois qu’un fichier est téléversé dans un bucket S3, via un pipeline S3 → Lambda → SNS.

## 🛠 Services AWS utilisés

| Service         | Rôle dans le projet                                                   |
|------------------|------------------------------------------------------------------------|
| **Amazon S3**     | Stockage d’objet déclenchant un événement lors de l’upload.            |
| **AWS Lambda**    | Fonction déclenchée par S3, qui publie un message dans le topic SNS.   |
| **Amazon SNS**    | Service de notification qui envoie l’email aux abonnés.               |
| **AWS IAM**       | Gestion des autorisations pour permettre à Lambda de publier sur SNS. |

## 🧪 Étapes de déploiement

### 1. Créer le bucket S3
- Accéder à S3 → Créer un bucket (ex: `sns-bucket-lab25`)
- Décochez l’option "Bloquer tout l'accès public" (facultatif)
- Validez la création
![](img/bucket/create-bucket1.png)
![](img/bucket/create-bucket2.png)
![](img/bucket/create-bucket3.png)
![](img/bucket/create-bucket4.png)
![](img/bucket/create-bucket5.png)
![](img/bucket/create-bucket6.png)

### 2. Créer la fonction Lambda
- Runtime : Python 3.12  
- Code source :

```python
import json, boto3, os
sns_client = boto3.client('sns')
TOPIC_ARN = os.environ['TOPIC_ARN']

def lambda_handler(event, context):
    bucket = event['Records'][0]['s3']['bucket']['name']
    key = event['Records'][0]['s3']['object']['key']
    message = f"Nouveau fichier ajouté : {key} dans le bucket {bucket}"
    sns_client.publish(
        TopicArn=TOPIC_ARN,
        Subject="Nouvel Upload S3",
        Message=message
    )
    return {'statusCode': 200, 'body': json.dumps('Notification envoyée avec succès')}
```

- Déployer la fonction
![](img/lambda/create-fonction1.png)
![](img/lambda/create-fonction2.png)
![](img/lambda/create-fonction3.png)
![](img/lambda/create-fonction4.png)
![](img/lambda/create-fonction5.png)

### 3. Créer un topic SNS
- Nom du sujet : `S3UploadTopic`
- Créer un abonnement email
- Confirmer l’abonnement reçu par email
![](img/sns/create-sns1.png)
![](img/sns/create-sns2.png)
![](img/sns/create-sns3.png)
![](img/sns/create-sns4.png)
![](img/sns/create-sns5.png)
![](img/sns/create-sns6.png)
![](img/sns/create-sns7.png)
![](img/sns/create-sns8.png)
![](img/sns/create-sns9.png)
![](img/sns/create-sns10.png)
![](img/sns/create-sns11.png)

### 4. Ajouter les autorisations et variables Lambda
- Ajouter la variable d’environnement `TOPIC_ARN`
- Ajouter cette stratégie inline dans IAM :

```json
{
  "Effect": "Allow",
  "Action": "sns:Publish",
  "Resource": "arn:aws:sns:REGION:ACCOUNT_ID:S3UploadTopic"
}
```
![](img/lambda/addarn-sns1.png)
![](img/lambda/addarn-sns2.png)
![](img/lambda/addarn-sns3.png)
![](img/lambda/addarn-sns4.png)
![](img/lambda/add-role1.png)
![](img/lambda/add-role1.png)
![](img/lambda/add-role2.png)
![](img/lambda/add-role3.png)
![](img/lambda/add-role4.png)
![](img/lambda/add-role5.png)
![](img/lambda/add-role6.png)
![](img/lambda/add-role7.png)
![](img/lambda/add-role1.png)

### 5. Connecter S3 à Lambda
- Aller dans S3 > Propriétés > Événements
- Créer un événement `PUT`
- Cible : fonction `notifierFunction`
![](img/lambda/add-event1.png)
![](img/lambda/add-event2.png)
![](img/lambda/add-event3.png)
![](img/lambda/add-event4.png)
![](img/lambda/add-event5.png)

### 6. Tester le système
- Upload un fichier dans S3
- Vérifier que :
  - Lambda est déclenchée
  - Un email est reçu via SNS
  - Les logs sont visibles dans CloudWatch
![](img/teste/teste1.png)
![](img/teste/teste2.png)
![](img/teste/teste3.png)
![](img/teste/teste4.png)
![](img/teste/teste5.png)
![](img/teste/teste5a.png)
![](img/teste/teste5b.png)
![](img/teste/teste6.png)
![](img/teste/teste7.png)
![](img/teste/teste8.png)
![](img/teste/teste9.png)

## 📸 Démo
Dossier ![](img/) contenant les preuves de test (mail reçu, logs, etc.).

## 🧠 Compétences développées
- Architecture événementielle serverless
- Intégration S3 + Lambda + SNS
- Permissions IAM
- Surveillance via CloudWatch Logs

## 📄 Rapport complet

📄 [Télécharger le rapport PDF complet](docs/rapport-lab.pdf)

## 👤 Auteur
**France Stanislas Bantantoula Koudissa**  
Etudiant en Maste Génie Informatique  
🔗 [LinkedIn](https://ci.linkedin.com/in/france-stanislas-bantantoula-koudissa-30245b254)

