<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Flashcards App</title>
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Poppins:wght@400;500;600&display=swap" rel="stylesheet">
    <style>
        :root {
            --primary-color: #5B21B6; /* Violet profond */
            --secondary-color: #8B5CF6; /* Violet plus clair */
            --accent-color: #10B981; /* Vert émeraude */
            --text-color: #374151; /* Gris foncé */
            --light-bg: #F9FAFB; /* Arrière-plan très clair */
            --card-bg: #FFFFFF; /* Fond des cartes blanc */
            --border-color: #E5E7EB; /* Couleur de bordure */
            --shadow-light: 0 4px 6px -1px rgba(0, 0, 0, 0.1), 0 2px 4px -1px rgba(0, 0, 0, 0.06);
            --shadow-medium: 0 10px 15px -3px rgba(0, 0, 0, 0.1), 0 4px 6px -2px rgba(0, 0, 0, 0.05);
        }

        body {
            font-family: 'Poppins', sans-serif;
            margin: 0;
            padding: 0;
            background-color: var(--light-bg);
            color: var(--text-color);
            line-height: 1.6;
            display: flex;
            min-height: 100vh;
            flex-direction: column;
        }

        /* NOUVEAU : En-tête fixe */
        .header {
            background-color: var(--card-bg);
            padding: 1rem 2rem;
            box-shadow: var(--shadow-light);
            display: flex;
            justify-content: space-between;
            align-items: center;
            position: sticky; /* Rendre l'en-tête collant en haut */
            top: 0;
            z-index: 1000;
        }

        .header-title {
            font-size: 1.5rem;
            font-weight: 600;
            color: var(--primary-color);
            margin: 0;
        }

        .header-nav {
            display: flex;
            gap: 1.5rem;
        }

        .header-nav a {
            text-decoration: none;
            color: var(--text-color);
            font-weight: 500;
            padding: 0.5rem 0;
            transition: color 0.3s ease;
        }

        .header-nav a:hover {
            color: var(--secondary-color);
        }
        
        .header-nav a.active {
            color: var(--primary-color);
            border-bottom: 2px solid var(--primary-color);
        }

        .container {
            flex-grow: 1; /* Permet au conteneur de prendre l'espace restant */
            max-width: 900px;
            margin: 2rem auto; /* Centre le conteneur principal */
            padding: 0 1.5rem;
        }

        .section-title {
            font-size: 2rem;
            font-weight: 600;
            color: var(--primary-color);
            margin-bottom: 1.5rem;
            text-align: center;
        }

        .button {
            display: inline-flex;
            align-items: center;
            justify-content: center;
            padding: 0.75rem 1.5rem;
            border-radius: 0.5rem;
            font-size: 1rem;
            font-weight: 500;
            cursor: pointer;
            transition: all 0.2s ease-in-out;
            border: none;
            text-decoration: none;
        }

        .button-primary {
            background-color: var(--primary-color);
            color: white;
            box-shadow: var(--shadow-light);
        }

        .button-primary:hover {
            background-color: var(--secondary-color);
            transform: translateY(-2px);
            box-shadow: var(--shadow-medium);
        }

        .button-secondary {
            background-color: var(--secondary-color);
            color: white;
            box-shadow: var(--shadow-light);
        }

        .button-secondary:hover {
            background-color: #6D28D9; /* Une nuance un peu plus foncée */
            transform: translateY(-2px);
            box-shadow: var(--shadow-medium);
        }

        .button-accent {
            background-color: var(--accent-color);
            color: white;
            box-shadow: var(--shadow-light);
        }

        .button-accent:hover {
            background-color: #059669; /* Une nuance un peu plus foncée */
            transform: translateY(-2px);
            box-shadow: var(--shadow-medium);
        }

        .button-outline {
            background-color: transparent;
            color: var(--primary-color);
            border: 2px solid var(--primary-color);
        }

        .button-outline:hover {
            background-color: var(--primary-color);
            color: white;
            transform: translateY(-2px);
            box-shadow: var(--shadow-medium);
        }

        .card {
            background-color: var(--card-bg);
            border-radius: 0.75rem;
            box-shadow: var(--shadow-light);
            padding: 1.5rem;
            margin-bottom: 1.5rem;
            border: 1px solid var(--border-color);
        }

        .card-list {
            display: flex;
            flex-direction: column;
            gap: 1rem;
        }

        .card-item {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 1rem;
            background-color: var(--light-bg);
            border-radius: 0.5rem;
            border: 1px solid var(--border-color);
        }

        .card-item-info h3 {
            margin: 0 0 0.25rem 0;
            font-size: 1.25rem;
            color: var(--text-color);
        }

        .card-item-info p {
            margin: 0;
            font-size: 0.9rem;
            color: #6B7280;
        }

        .card-item-actions {
            display: flex;
            gap: 0.75rem;
        }

        .form-group {
            margin-bottom: 1rem;
        }

        .form-group label {
            display: block;
            margin-bottom: 0.5rem;
            font-weight: 500;
            color: var(--text-color);
        }

        .form-group input[type="text"],
        .form-group textarea {
            width: 100%;
            padding: 0.75rem;
            border: 1px solid var(--border-color);
            border-radius: 0.5rem;
            font-family: 'Poppins', sans-serif;
            font-size: 1rem;
            box-sizing: border-box; /* S'assure que padding n'augmente pas la largeur */
        }

        .form-group textarea {
            min-height: 100px;
            resize: vertical;
        }

        .form-actions {
            display: flex;
            justify-content: flex-end;
            gap: 1rem;
            margin-top: 1.5rem;
        }

        .flashcard-display {
            display: flex;
            flex-direction: column;
            align-items: center;
            gap: 1.5rem;
            text-align: center;
        }

        .flashcard-wrapper {
            perspective: 1000px;
            width: 100%;
            max-width: 500px;
        }

        .flashcard {
            background-color: var(--card-bg);
            border-radius: 0.75rem;
            box-shadow: var(--shadow-medium);
            padding: 2rem;
            min-height: 200px;
            width: 100%;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 1.5rem;
            font-weight: 500;
            cursor: pointer;
            transition: transform 0.6s;
            transform-style: preserve-3d;
            position: relative;
        }

        .flashcard.flipped {
            transform: rotateY(180deg);
        }

        .flashcard-face {
            position: absolute;
            width: 100%;
            height: 100%;
            top: 0;
            left: 0;
            -webkit-backface-visibility: hidden; /* Masque l'arrière-plan pendant le flip */
            backface-visibility: hidden;
            display: flex;
            align-items: center;
            justify-content: center;
            padding: 2rem;
            box-sizing: border-box;
            word-wrap: break-word; /* Important pour les longs textes */
        }
        
        .flashcard-front, .flashcard-back {
            background-color: var(--card-bg);
            border-radius: 0.75rem;
        }


        .flashcard-back {
            transform: rotateY(180deg);
        }

        .flashcard-controls {
            display: flex;
            gap: 1rem;
        }

        .compare-section {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 2rem;
            margin-top: 2rem;
        }

        .compare-box {
            flex: 1;
        }

        .diff-output {
            margin-top: 1.5rem;
            padding: 1rem;
            background-color: #f7fafc; 
            border: 1px solid var(--border-color);
            border-radius: 0.5rem;
            white-space: pre-wrap; /* Preserve spaces and breaks */
        }

        .diff-output mark.added {
            background-color: #d1fae5; /* Vert très clair */
            color: #065F46; /* Vert foncé */
        }

        .diff-output mark.removed {
            background-color: #fee2e2; /* Rouge très clair */
            color: #991B1B; /* Rouge foncé */
            text-decoration: line-through;
        }

        /* Responsive adjustments */
        @media (max-width: 768px) {
            .header {
                flex-direction: column;
                gap: 1rem;
                padding: 1rem;
            }

            .header-nav {
                width: 100%;
                justify-content: space-around;
            }

            .container {
                margin: 1rem auto;
                padding: 0 1rem;
            }

            .section-title {
                font-size: 1.5rem;
            }

            .card-item {
                flex-direction: column;
                align-items: flex-start;
                gap: 0.75rem;
            }

            .card-item-actions {
                width: 100%;
                justify-content: space-around;
            }

            .compare-section {
                grid-template-columns: 1fr;
                gap: 1rem;
            }
        }
    </style>
    <script src="https://cdn.jsdelivr.net/npm/diff@5.1.0/dist/diff.min.js"></script>
    <script>
        document.addEventListener('DOMContentLoaded', () => {
            // Initialisation des données des listes et cartes
            let currentView = 'home';
            let currentListId = null;
            let currentCardIndex = 0;
            let currentCards = []; // Cartes de la liste en cours de révision

            // Conteneur de toutes les vues, pour ne pas avoir à les recréer à chaque fois
            const views = {
                home: document.createElement('div'),
                lists: document.createElement('div'),
                createList: document.createElement('div'),
                manageList: document.createElement('div'),
                revise: document.createElement('div'),
                createCard: document.createElement('div'),
                editCard: document.createElement('div'),
                compare: document.createElement('div')
            };

            // Fonction utilitaire pour charger les données
            const loadData = () => JSON.parse(localStorage.getItem('flashcardsData')) || {};
            const saveData = (data) => localStorage.setItem('flashcardsData', JSON.stringify(data));
            
            // Initialise le contenu principal une seule fois
            document.getElementById('app-content').innerHTML = `
                <div class="container" id="main-container"></div>
            `;
            const mainContainer = document.getElementById('main-container');


            // --- Fonctions utilitaires ---
            const shuffleArray = (array) => {
                for (let i = array.length - 1; i > 0; i--) {
                    const j = Math.floor(Math.random() * (i + 1));
                    [array[i], array[j]] = [array[j], array[i]];
                }
            };


            // --- Rendus des Vues ---
            const renderHomeView = () => {
                const data = loadData();
                const numCourses = Object.keys(data).length;
                let numTotalCards = 0;
                for (const listId in data) {
                    numTotalCards += data[listId].cards.length;
                }

                views.home.innerHTML = `
                    <section class="card">
                        <h2 class="section-title">Bienvenue ! 👋</h2>
                        <p style="text-align: center; margin-bottom: 2rem;">Apprends efficacement avec ton système de flashcards.</p>
                        <div style="display: flex; justify-content: space-around; gap: 1rem;">
                            <div class="card" style="flex: 1; text-align: center;">
                                <h3 style="font-size: 2.5rem; color: var(--primary-color);">${numCourses}</h3>
                                <p>Cours créés</p>
                            </div>
                            <div class="card" style="flex: 1; text-align: center;">
                                <h3 style="font-size: 2.5rem; color: var(--primary-color);">${numTotalCards}</h3>
                                <p>Cartes totales</p>
                            </div>
                        </div>
                        <div style="text-align: center; margin-top: 2rem;">
                            <button class="button button-primary" onclick="navigateTo('lists')">Voir mes listes</button>
                        </div>
                    </section>
                `;
            };

            const renderListsView = () => {
                const data = loadData();
                views.lists.innerHTML = `
                    <h2 class="section-title">Mes Listes de Flashcards</h2>
                    <p style="text-align: center; margin-bottom: 2rem;">Sélectionne une liste à réviser ou à gérer.</p>
                    <div class="card-list">
                        ${Object.keys(data).length === 0 ? '<p style="text-align: center;">Aucune liste créée pour le moment. Créez-en une !</p>' : ''}
                        ${Object.keys(data).map(listId => `
                            <div class="card-item">
                                <div class="card-item-info">
                                    <h3>${data[listId].name}</h3>
                                    <p>${data[listId].cards.length} carte${data[listId].cards.length > 1 ? 's' : ''}</p>
                                </div>
                                <div class="card-item-actions">
                                    <button class="button button-secondary" onclick="navigateTo('revise', '${listId}')">Réviser</button>
                                    <button class="button button-accent" onclick="navigateTo('manageList', '${listId}')">Gérer</button>
                                    <button class="button button-outline" onclick="deleteList('${listId}')">Supprimer</button>
                                </div>
                            </div>
                        `).join('')}
                    </div>
                    <div style="text-align: center; margin-top: 2rem;">
                        <button class="button button-primary" onclick="navigateTo('createList')">+ Créer une nouvelle liste</button>
                    </div>
                `;
            };

            const renderCreateListView = () => {
                views.createList.innerHTML = `
                    <h2 class="section-title">Créer une nouvelle liste</h2>
                    <div class="card">
                        <div class="form-group">
                            <label for="listName">Nom de la liste</label>
                            <input type="text" id="listName" placeholder="Ex: Droit Administratif - Chapitre 1">
                        </div>
                        <div class="form-actions">
                            <button class="button button-outline" onclick="navigateTo('lists')">Annuler</button>
                            <button class="button button-primary" onclick="saveNewList()">Créer la liste</button>
                        </div>
                    </div>
                `;
            };

            const renderManageListView = (listId) => {
                const data = loadData();
                const list = data[listId];
                if (!list) { navigateTo('lists'); return; }

                views.manageList.innerHTML = `
                    <h2 class="section-title">Gérer : ${list.name}</h2>
                    <div class="card">
                        <div class="form-group">
                            <label for="editListName">Renommer la liste</label>
                            <input type="text" id="editListName" value="${list.name}">
                        </div>
                        <div class="form-actions" style="justify-content: space-between;">
                            <button class="button button-outline" onclick="navigateTo('lists')">Retour aux listes</button>
                            <button class="button button-primary" onclick="updateListName('${listId}')">Renommer</button>
                        </div>
                    </div>

                    <h3 style="margin-top: 2rem; margin-bottom: 1rem; font-size: 1.5rem; color: var(--text-color);">Cartes de la liste (${list.cards.length})</h3>
                    <div class="card-list">
                        ${list.cards.length === 0 ? '<p style="text-align: center;">Aucune carte dans cette liste. Ajoutez-en une !</p>' : ''}
                        ${list.cards.map((card, index) => `
                            <div class="card-item">
                                <div class="card-item-info">
                                    <h3>${card.front.substring(0, 50)}${card.front.length > 50 ? '...' : ''}</h3>
                                    <p>Recto / Verso</p>
                                </div>
                                <div class="card-item-actions">
                                    <button class="button button-secondary" onclick="navigateTo('editCard', '${listId}', ${index})">Modifier</button>
                                    <button class="button button-outline" onclick="deleteCard('${listId}', ${index})">Supprimer</button>
                                </div>
                            </div>
                        `).join('')}
                    </div>
                    <div style="text-align: center; margin-top: 2rem;">
                        <button class="button button-primary" onclick="navigateTo('createCard', '${listId}')">+ Ajouter une carte</button>
                    </div>
                `;
            };

            const renderReviseView = (listId) => {
                const data = loadData();
                const list = data[listId];
                if (!list || list.cards.length === 0) {
                    views.revise.innerHTML = `
                        <h2 class="section-title">Révision</h2>
                        <div class="card" style="text-align: center;">
                            <p>Cette liste est vide ou n'existe pas. Retournez à vos listes.</p>
                            <button class="button button-primary" onclick="navigateTo('lists')">Retour aux listes</button>
                        </div>
                    `;
                    return;
                }

                if (currentCards.length === 0) {
                    currentCards = [...list.cards]; // Copie des cartes pour la révision
                    shuffleArray(currentCards); // Mélange aléatoire
                    currentCardIndex = 0;
                }

                if (currentCardIndex >= currentCards.length) {
                    views.revise.innerHTML = `
                        <h2 class="section-title">Révision terminée ! 🎉</h2>
                        <div class="card" style="text-align: center;">
                            <p>Vous avez révisé toutes les cartes de cette liste.</p>
                            <button class="button button-primary" onclick="navigateTo('lists')">Retour aux listes</button>
                        </div>
                    `;
                    currentCards = []; // Réinitialiser pour la prochaine révision
                    return;
                }

                const card = currentCards[currentCardIndex];
                views.revise.innerHTML = `
                    <h2 class="section-title">Révision : ${list.name}</h2>
                    <p style="text-align: center; margin-bottom: 2rem;">Carte ${currentCardIndex + 1} / ${currentCards.length}</p>
                    <div class="flashcard-display">
                        <div class="flashcard-wrapper">
                            <div class="flashcard" id="currentFlashcard">
                                <div class="flashcard-face flashcard-front">${card.front}</div>
                                <div class="flashcard-face flashcard-back">${card.back}</div>
                            </div>
                        </div>
                        <div class="flashcard-controls">
                            <button class="button button-secondary" id="showAnswerBtn">Afficher la réponse</button>
                            <button class="button button-primary" id="nextCardBtn" style="display: none;">Carte suivante</button>
                        </div>
                    </div>
                    <div style="text-align: center; margin-top: 2rem;">
                         <button class="button button-outline" onclick="navigateTo('lists')">Arrêter la révision</button>
                    </div>
                `;

                const flashcardElement = views.revise.querySelector('#currentFlashcard');
                const showAnswerBtn = views.revise.querySelector('#showAnswerBtn');
                const nextCardBtn = views.revise.querySelector('#nextCardBtn');
                
                // Gérer le flip de la carte au clic
                flashcardElement.addEventListener('click', () => {
                     flashcardElement.classList.toggle('flipped');
                });


                showAnswerBtn.onclick = () => {
                    flashcardElement.classList.add('flipped');
                    showAnswerBtn.style.display = 'none';
                    nextCardBtn.style.display = 'inline-flex';
                };

                nextCardBtn.onclick = () => {
                    currentCardIndex++;
                    renderReviseView(listId);
                };
            };

            const renderCreateCardView = (listId) => {
                views.createCard.innerHTML = `
                    <h2 class="section-title">Ajouter une carte</h2>
                    <div class="card">
                        <div class="form-group">
                            <label for="cardFront">Recto de la carte</label>
                            <textarea id="cardFront" placeholder="Ex: Qu'est-ce que le service public ?"></textarea>
                        </div>
                        <div class="form-group">
                            <label for="cardBack">Verso de la carte</label>
                            <textarea id="cardBack" placeholder="Ex: Activité d'intérêt général assurée ou assumée par une personne publique."></textarea>
                        </div>
                        <div class="form-actions">
                            <button class="button button-outline" onclick="navigateTo('manageList', '${listId}')">Annuler</button>
                            <button class="button button-primary" onclick="saveNewCard('${listId}')">Ajouter la carte</button>
                        </div>
                    </div>
                `;
            };

            const renderEditCardView = (listId, cardIndex) => {
                const data = loadData();
                const card = data[listId].cards[cardIndex];
                if (!card) { navigateTo('manageList', listId); return; }

                views.editCard.innerHTML = `
                    <h2 class="section-title">Modifier la carte</h2>
                    <div class="card">
                        <div class="form-group">
                            <label for="editCardFront">Recto de la carte</label>
                            <textarea id="editCardFront">${card.front}</textarea>
                        </div>
                        <div class="form-group">
                            <label for="editCardBack">Verso de la carte</label>
                            <textarea id="editCardBack">${card.back}</textarea>
                        </div>
                        <div class="form-actions">
                            <button class="button button-outline" onclick="navigateTo('manageList', '${listId}')">Annuler</button>
                            <button class="button button-primary" onclick="updateCard('${listId}', ${cardIndex})">Mettre à jour</button>
                        </div>
                    </div>
                `;
            };

            const renderCompareView = () => {
                views.compare.innerHTML = `
                    <h2 class="section-title">Comparateur de texte</h2>
                    <p style="text-align: center; margin-bottom: 2rem;">Comparez deux textes pour identifier les différences.</p>
                    <div class="compare-section">
                        <div class="compare-box form-group">
                            <label for="text1">Texte Ancien</label>
                            <textarea id="text1" rows="10" placeholder="Entrez le texte ancien ici..."></textarea>
                        </div>
                        <div class="compare-box form-group">
                            <label for="text2">Texte Récent</label>
                            <textarea id="text2" rows="10" placeholder="Entrez le texte récent ici..."></textarea>
                        </div>
                    </div>
                    <div style="text-align: center; margin-top: 1.5rem;">
                        <button class="button button-primary" onclick="compareTexts()">Comparer les textes</button>
                    </div>
                    <div id="diffOutput" class="diff-output" style="display: none;"></div>
                `;
            };

            // --- Fonctions de Navigation et Logique ---
            window.navigateTo = (viewName, ...args) => {
                currentView = viewName;
                mainContainer.innerHTML = ''; // Vide le contenu actuel

                // Met à jour la classe active dans la navigation
                document.querySelectorAll('.header-nav a').forEach(link => {
                    link.classList.remove('active');
                });
                
                // Logique pour déterminer quel lien activer
                if (viewName === 'home') {
                    document.querySelector('.header-nav a[data-view="home"]').classList.add('active');
                } else if (viewName === 'compare') {
                    document.querySelector('.header-nav a[data-view="compare"]').classList.add('active');
                } else {
                    // Les autres vues sont liées aux listes
                    document.querySelector('.header-nav a[data-view="lists"]').classList.add('active');
                }


                switch (viewName) {
                    case 'home':
                        renderHomeView();
                        break;
                    case 'lists':
                        renderListsView();
                        break;
                    case 'createList':
                        renderCreateListView();
                        break;
                    case 'manageList':
                        currentListId = args[0];
                        renderManageListView(currentListId);
                        break;
                    case 'revise':
                        currentListId = args[0];
                        // Réinitialiser les cartes pour une nouvelle session
                        currentCards = [];
                        currentCardIndex = 0; 
                        renderReviseView(currentListId); 
                        break;
                    case 'createCard':
                        currentListId = args[0];
                        renderCreateCardView(currentListId);
                        break;
                    case 'editCard':
                        currentListId = args[0];
                        renderEditCardView(currentListId, args[1]);
                        break;
                    case 'compare':
                        renderCompareView();
                        break;
                }
                mainContainer.appendChild(views[currentView]);
            };

            window.saveNewList = () => {
                const listName = document.getElementById('listName').value.trim();
                if (listName) {
                    const data = loadData();
                    const newListId = 'list_' + Date.now();
                    data[newListId] = { name: listName, cards: [] };
                    saveData(data);
                    navigateTo('lists');
                } else {
                    alert('Le nom de la liste ne peut pas être vide.');
                }
            };

            window.updateListName = (listId) => {
                const newName = document.getElementById('editListName').value.trim();
                if (newName) {
                    const data = loadData();
                    if (data[listId]) {
                        data[listId].name = newName;
                        saveData(data);
                        navigateTo('manageList', listId); // Recharge la vue pour afficher le nouveau nom
                    }
                } else {
                    alert('Le nom de la liste ne peut pas être vide.');
                }
            };

            window.deleteList = (listId) => {
                if (confirm('Êtes-vous sûr de vouloir supprimer cette liste et toutes ses cartes ?')) {
                    const data = loadData();
                    delete data[listId];
                    saveData(data);
                    navigateTo('lists');
                }
            };

            window.saveNewCard = (listId) => {
                const cardFront = document.getElementById('cardFront').value.trim();
                const cardBack = document.getElementById('cardBack').value.trim();
                if (cardFront && cardBack) {
                    const data = loadData();
                    if (data[listId]) {
                        data[listId].cards.push({ front: cardFront, back: cardBack });
                        saveData(data);
                        navigateTo('manageList', listId);
                    }
                } else {
                    alert('Le recto et le verso de la carte ne peuvent pas être vides.');
                }
            };

            window.updateCard = (listId, cardIndex) => {
                const newFront = document.getElementById('editCardFront').value.trim();
                const newBack = document.getElementById('editCardBack').value.trim();
                if (newFront && newBack) {
                    const data = loadData();
                    if (data[listId] && data[listId].cards[cardIndex]) {
                        data[listId].cards[cardIndex] = { front: newFront, back: newBack };
                        saveData(data);
                        navigateTo('manageList', listId);
                    }
                } else {
                    alert('Le recto et le verso de la carte ne peuvent pas être vides.');
                }
            };

            window.deleteCard = (listId, cardIndex) => {
                if (confirm('Êtes-vous sûr de vouloir supprimer cette carte ?')) {
                    const data = loadData();
                    if (data[listId] && data[listId].cards[cardIndex]) {
                        data[listId].cards.splice(cardIndex, 1);
                        saveData(data);
                        navigateTo('manageList', listId);
                    }
                }
            };
            
            // --- Logique du Comparateur ---
            window.compareTexts = () => {
                const text1 = document.getElementById('text1').value;
                const text2 = document.getElementById('text2').value;
                const diffOutput = document.getElementById('diffOutput');

                if (!text1 || !text2) {
                    alert("Veuillez entrer les deux textes à comparer.");
                    diffOutput.style.display = 'none';
                    return;
                }

                // Utilisation de diffWords pour une comparaison au niveau des mots
                const diff = Diff.diffWords(text1, text2);
                let output = document.createElement('div');
                
                diff.forEach((part) => {
                    // On ne crée pas de marque pour les parties non modifiées
                    if (!part.added && !part.removed) {
                        output.appendChild(document.createTextNode(part.value));
                    } else {
                        const span = document.createElement('mark');
                        span.className = part.added ? 'added' : 'removed';
                        span.appendChild(document.createTextNode(part.value));
                        output.appendChild(span);
                    }
                });
                
                // Affichage du résultat
                diffOutput.innerHTML = '';
                diffOutput.appendChild(output);
                diffOutput.style.display = 'block';
            };


            // --- Initialisation ---
            // Initialisation avec une liste par défaut si le stockage est vide
            if (Object.keys(loadData()).length === 0) {
                 const initialData = {
                    'list_demo': {
                        name: 'Exemple de Droit',
                        cards: [
                            { front: "Qu'est-ce que le principe de légalité ?", back: "L'administration doit agir conformément aux règles de droit." },
                            { front: "Définition du contrat administratif.", back: "Contrat conclu entre au moins une personne publique, portant sur un objet d'intérêt public." }
                        ]
                    }
                };
                saveData(initialData);
            }
            
            // Démarrer sur la page d'accueil
            navigateTo('home');
        });
    </script>
</head>
<body>
    
    <header class="header">
        <h1 class="header-title">📚 Flashcards App</h1>
        <nav class="header-nav">
            <a href="#" data-view="home" onclick="event.preventDefault(); navigateTo('home')">Accueil</a>
            <a href="#" data-view="lists" onclick="event.preventDefault(); navigateTo('lists')">Mes Listes</a>
            <a href="#" data-view="compare" onclick="event.preventDefault(); navigateTo('compare')">Comparateur</a>
        </nav>
    </header>

    <div id="app-content">
        </div>

</body>
</html>
