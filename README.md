Classification des Fleurs avec TensorFlow
Ce projet fait partie de la formation Udemy - Keras Deep Learning & Generative Adversarial Networks (GAN) 2023-6. Il implémente un modèle d'apprentissage profond pour classer des images de fleurs en utilisant TensorFlow et Keras. Le modèle est entraîné sur un ensemble de données d'images de fleurs et peut les classer en 5 catégories différentes.

Prérequis
Avant de lancer le code, assurez-vous d'avoir les éléments suivants installés :

Python 3.x
Google Colab ou un autre environnement Python avec TensorFlow installé
Pour installer TensorFlow localement, utilisez la commande suivante :


pip install tensorflow
Jeu de données
L'ensemble de données utilisé dans ce projet est une collection d'images de fleurs, classées en 5 catégories différentes. Il est supposé que l'ensemble de données est stocké dans Google Drive. La structure du répertoire devrait ressembler à ceci :


flowers_datasets/
    ├── flowers/
    │   ├── train/
    │   │   ├── classe1/
    │   │   ├── classe2/
    │   │   ├── ...
    │   └── test/
    │       ├── classe1/
    │       ├── classe2/
    │       ├── ...
Vous devez monter votre Google Drive et télécharger l'ensemble de données dans le répertoire spécifié pour que le script fonctionne.

Configuration
Monter Google Drive : Ce code utilise Google Colab, donc la première étape consiste à monter votre Google Drive pour accéder à l'ensemble de données.


from google.colab import drive
drive.mount('/content/drive')
Copier l'Ensemble de Données : L'ensemble de données est copié depuis Google Drive vers l'espace de travail Colab pour un traitement ultérieur.

!cp -r '/content/drive/My Drive/flowers_datasets' /content/
print("dossier copié avec succès.")
Explication du Code
Préparation des Données
Les données sont préparées en utilisant ImageDataGenerator, qui applique des techniques d'augmentation des données pour améliorer la robustesse du modèle :

train_datagen = ImageDataGenerator(
    rescale=1./255,
    horizontal_flip=True,
    rotation_range=20,
    width_shift_range=0.2,
    height_shift_range=0.2,
)

test_datagen = ImageDataGenerator(
    rescale=1./255,
    horizontal_flip=True,
    rotation_range=20,
    width_shift_range=0.2,
    height_shift_range=0.2,
)
Définition du Modèle
Un simple réseau de neurones convolutifs (CNN) est défini en utilisant l'API Sequential de Keras. Le modèle se compose de plusieurs couches convolutives suivies de couches de sous-échantillonnage, d'un aplatissement et de couches denses :


model = keras.Sequential()
model.add(layers.Conv2D(32, (3, 3), activation='relu', input_shape=(100, 100, 3), padding='same'))
model.add(layers.MaxPooling2D(2, 2))

model.add(layers.Conv2D(64, (3, 3), activation='relu', padding='same'))
model.add(layers.MaxPooling2D(2, 2))

model.add(lers.Conv2D(128, (3, 3), activation='relu', padding='same'))
model.add(layers.MaxPooling2D(2, 2))

model.add(layers.Flatten())
model.add(layers.Dense(512, activation='relu'))
model.add(layers.Dense(5, activation='softmax'))  # 5 classes pour la classification
Flux de Données
Nous utilisons la méthode flow_from_directory pour charger les images des répertoires d'entraînement et de test :


training_iterator = train_datagen.flow_from_directory('/content/flowers_datasets/flowers/train', batch_size=64, target_size=(100, 100))
testing_iterator = test_datagen.flow_from_directory('/content/flowers_datasets/flowers/test', batch_size=64, target_size=(100, 100))
Compilation et Entraînement du Modèle
Le modèle est compilé avec la fonction de perte categorical_crossentropy et l'optimiseur Adam, puis il est entraîné pendant 8 époques :


model.compile(loss='categorical_crossentropy', metrics=['accuracy'], optimizer='adam')
history = model.fit(training_iterator, validation_data=testing_iterator, epochs=8)
Sauvegarde du Modèle
Une fois que le modèle est entraîné, il est sauvegardé dans Google Drive :


model.save('/content/drive/My Drive/flowze_baseline_model.h5')
Étiquettes des Classes
Les étiquettes des classes (pour les catégories de fleurs) sont récupérées depuis training_iterator :
class_labels = training_iterator.class_indices
print(class_labels)

