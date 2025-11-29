<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Flashcards + Comparateur</title>
    <style>
        /* CSS GENERAL & LAYOUT */
        * { margin: 0; padding: 0; box-sizing: border-box; }
        body { 
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; 
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%); 
            min-height: 100vh; 
            display: flex; 
        } 
        
        /* SIDEBAR FIXE (MODIFICATIONS MINIMALES) */
        .sidebar { 
            width: 280px; 
            background: white; 
            box-shadow: 2px 0 10px rgba(0, 0, 0, 0.1); 
            display: flex; 
            flex-direction: column; 
            position: fixed; 
            height: 100vh; 
            left: 0; 
            top: 0; 
            z-index: 100; 
        } 
        
        /* SUPPRESSION : Styles inutiles pour le mode caché (la barre est toujours visible) */
        /* .sidebar.hidden { transform: translateX(-280px); } */
        
        .sidebar-header { padding: 30px 20px; background: linear-gradient(135deg, #667eea 0%, #764ba2 100%); color: white; }
        .sidebar-header h1 { font-size: 22px; margin-bottom: 5px; }
        .sidebar-header p { font-size: 13px; opacity: 0.9; }
        .sidebar-nav { padding: 20px; border-bottom: 2px solid #e0e0e0; }
        .nav-item { padding: 12px 15px; margin-bottom: 8px; border-radius: 8px; cursor: pointer; transition: all 0.3s; display: flex; align-items: center; gap: 10px; font-weight: 500; }
        .nav-item:hover { background: #f5f7fa; }
        .nav-item.active { background: linear-gradient(135deg, #667eea 0%, #764ba2 100%); color: white; }
        .folder-section { flex: 1; overflow-y: auto; padding: 20px; }
        .folder-section-header { display: flex; justify-content: space-between; align-items: center; margin-bottom: 15px; }
        .folder-section-header h3 { font-size: 14px; color: #666; text-transform: uppercase; letter-spacing: 0.5px; }
        .folder-section-buttons { display: flex; gap: 5px; }
        .btn-new-folder, .btn-new-category { background: #667eea; color: white; border: none; padding: 6px 12px; border-radius: 6px; cursor: pointer; font-size: 12px; font-weight: 600; transition: all 0.3s; }
        .btn-new-folder:hover, .btn-new-category:hover { background: #5568d3; }
        .btn-new-category { background: #11998e; }
        .btn-new-category:hover { background: #0f8778; }
        .category-item { margin-bottom: 10px; }
        .category-header { display: flex; align-items: center; justify-content: space-between; padding: 8px 10px; background: #f5f7fa; border-radius: 6px; cursor: pointer; transition: all 0.3s; }
        .category-header:hover { background: #e8ebff; }
        .category-header.active { background: linear-gradient(135deg, #11998e 0%, #38ef7d 100%); color: white; }
        .category-left { display: flex; align-items: center; gap: 8px; flex: 1; }
        .category-toggle { font-size: 12px; transition: transform 0.3s; }
        .category-toggle.open { transform: rotate(90deg); }
        .category-name { font-weight: 600; font-size: 14px; }
        .category-count { background: rgba(102, 126, 234, 0.2); padding: 2px 8px; border-radius: 10px; font-size: 11px; font-weight: 600; color: #667eea; margin-left: 5px; }
        .category-header.active .category-count { background: rgba(255, 255, 255, 0.3); color: white; }
        .category-actions { display: flex; gap: 5px; opacity: 0; transition: all 0.3s; }
        .category-header:hover .category-actions { opacity: 1; }
        .btn-edit-category, .btn-delete-category { background: none; border: none; color: #999; cursor: pointer; font-size: 14px; padding: 0; width: 20px; height: 20px; display: flex; align-items: center; justify-content: center; transition: all 0.3s; }
        .category-header.active .btn-edit-category, .category-header.active .btn-delete-category { color: rgba(255, 255, 255, 0.8); }
        .btn-edit-category:hover { color: #667eea; }
        .category-header.active .btn-edit-category:hover { color: white; }
        .btn-delete-category:hover { color: #ff6b6b; }
        .category-header.active .btn-delete-category:hover { color: #ffcccc; }
        .category-folders { padding-left: 15px; margin-top: 5px; display: none; }
        .category-folders.open { display: block; }
        .folder-item { padding: 10px 15px; margin-bottom: 5px; border-radius: 6px; cursor: pointer; transition: all 0.3s; display: flex; align-items: center; justify-content: space-between; }
        .folder-item:hover { background: #f5f7fa; }
        .folder-item.active { background: #e8ebff; color: #667eea; font-weight: 600; }
        .folder-info { display: flex; align-items: center; gap: 8px; flex: 1; }
        .folder-badge { background: rgba(102, 126, 234, 0.2); padding: 2px 8px; border-radius: 10px; font-size: 11px; font-weight: 600; color: #667eea; }
        .folder-item.active .folder-badge { background: rgba(102, 126, 234, 0.3); }
        .folder-actions { display: flex; gap: 5px; opacity: 0; transition: all 0.3s; }
        .folder-item:hover .folder-actions { opacity: 1; }
        .btn-edit-folder, .btn-delete-folder { background: none; border: none; color: #999; cursor: pointer; font-size: 14px; padding: 0; width: 20px; height: 20px; display: flex; align-items: center; justify-content: center; transition: all 0.3s; }
        .btn-edit-folder:hover { color: #667eea; }
        .btn-delete-folder:hover { color: #ff6b6b; }
        
        /* SUPPRESSION : Styles pour le bouton de bascule inutile */
        .toggle-sidebar-btn { display: none; } 
        
        /* CONTENU PRINCIPAL */
        .main-content { 
            margin-left: 280px; /* GARDE CETTE MARGE FIXE pour le menu */
            flex: 1; 
            padding: 40px; 
            overflow-y: auto; 
            height: 100vh; 
            transition: none; /* Désactive la transition de marge */
        }
        
        /* SUPPRESSION : Style inutile pour le mode caché */
        /* .main-content.sidebar-hidden { margin-left: 0; } */
        
        /* RESTE DU CSS (NON MODIFIÉ) */
        .container { background: white; border-radius: 20px; box-shadow: 0 20px 60px rgba(0, 0, 0, 0.3); padding: 40px; max-width: 800px; margin: 0 auto; }
        .app-header { background: white; border-radius: 20px 20px 0 0; box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1); padding: 20px 40px; max-width: 800px; margin: 0 auto 20px auto; display: flex; justify-content: space-between; align-items: center; }
        .app-header h1 { margin: 0; font-size: 22px; color: #333; }
        .app-header p { margin: 0; font-size: 13px; color: #666; }
        .btn-comparator { background: linear-gradient(135deg, #11998e 0%, #38ef7d 100%); color: white; border: none; border-radius: 8px; padding: 10px 16px; font-weight: 600; cursor: pointer; display: flex; align-items: center; gap: 8px; transition: all 0.3s; }
        .btn-comparator:hover { transform: translateY(-2px); box-shadow: 0 5px 15px rgba(17, 153, 142, 0.4); }
        .home-page { text-align: center; }
        .home-hero { padding: 60px 40px; }
        .home-hero h1 { font-size: 48px; color: #333; margin-bottom: 20px; }
        .home-hero p { font-size: 18px; color: #666; line-height: 1.6; margin-bottom: 10px; }
        .stats-grid { display: grid; grid-template-columns: repeat(auto-fit, minmax(200px, 1fr)); gap: 20px; margin-top: 40px; }
        .stat-card { background: linear-gradient(135deg, #f5f7fa 0%, #c3cfe2 100%); padding: 30px; border-radius: 15px; box-shadow: 0 4px 15px rgba(0, 0, 0, 0.1); }
        .stat-card-value { font-size: 36px; font-weight: 700; color: #667eea; margin-bottom: 10px; }
        .stat-card-label { font-size: 14px; color: #666; font-weight: 500; }
        .quick-actions { margin-top: 40px; display: flex; gap: 15px; justify-content: center; flex-wrap: wrap; }
        .btn-action { background: linear-gradient(135deg, #667eea 0%, #764ba2 100%); color: white; padding: 15px 30px; border: none; border-radius: 10px; font-size: 16px; font-weight: 600; cursor: pointer; transition: all 0.3s; }
        .btn-action:hover { transform: translateY(-2px); box-shadow: 0 5px 15px rgba(102, 126, 234, 0.4); }
        .btn-action-secondary { background: linear-gradient(135deg, #11998e 0%, #38ef7d 100%); }
        .btn-action-secondary:hover { box-shadow: 0 5px 15px rgba(17, 153, 142, 0.4); }
        .recent-courses { margin-top: 50px; text-align: left; }
        .recent-courses h2 { font-size: 24px; color: #333; margin-bottom: 20px; }
        .course-list { display: flex; flex-direction: column; gap: 15px; }
        .course-card { background: #f5f7fa; padding: 20px; border-radius: 12px; cursor: pointer; transition: all 0.3s; display: flex; justify-content: space-between; align-items: center; }
        .course-card:hover { background: #e8ebff; transform: translateX(5px); }
        .course-card-left { flex: 1; }
        .course-card-title { font-size: 18px; font-weight: 600; color: #333; margin-bottom: 5px; }
        .course-card-info { font-size: 14px; color: #666; }
        .course-card-arrow { font-size: 24px; color: #667eea; }
        .lists-page h2 { color: #333; margin-bottom: 10px; font-size: 28px; display: inline-block; }
        .lists-page-header { display: flex; align-items: center; gap: 10px; margin-bottom: 10px; }
        .btn-edit-page-title { background: none; border: none; color: #667eea; cursor: pointer; font-size: 20px; padding: 5px; transition: all 0.3s; }
        .btn-edit-page-title:hover { color: #5568d3; transform: scale(1.1); }
        .lists-page-subtitle { color: #666; font-size: 14px; margin-bottom: 30px; }
        .lists-grid { display: flex; flex-direction: column; gap: 15px; margin-bottom: 30px; }
        .list-card { background: linear-gradient(135deg, #f5f7fa 0%, #c3cfe2 100%); padding: 25px 30px; border-radius: 15px; transition: all 0.3s; box-shadow: 0 4px 15px rgba(0, 0, 0, 0.1); position: relative; display: flex; justify-content: space-between; align-items: center; }
        .list-card:hover { transform: translateX(5px); box-shadow: 0 8px 25px rgba(0, 0, 0, 0.15); }
        .list-card-left { flex: 1; }
        .list-card-title { font-size: 22px; font-weight: 600; color: #333; margin-bottom: 8px; }
        .list-card-count { font-size: 14px; color: #666; margin-bottom: 5px; }
        .list-card-progress { font-size: 13px; color: #667eea; font-weight: 600; }
        .list-card-actions { display: flex; gap: 12px; }
        .btn-list-action { padding: 15px 30px; border: none; border-radius: 8px; font-size: 16px; font-weight: 600; cursor: pointer; transition: all 0.3s; color: white; min-width: 140px; }
        .btn-study { background: linear-gradient(135deg, #667eea 0%, #764ba2 100%); }
        .btn-study:hover { transform: translateY(-2px); box-shadow: 0 5px 15px rgba(102, 126, 234, 0.4); }
        .btn-manage { background: linear-gradient(135deg, #f5a623 0%, #f7b733 100%); }
        .btn-manage:hover { transform: translateY(-2px); box-shadow: 0 5px 15px rgba(245, 166, 35, 0.4); }
        .list-card-buttons { position: absolute; top: 10px; right: 10px; display: flex; gap: 5px; opacity: 0; transition: all 0.3s; }
        .list-card:hover .list-card-buttons { opacity: 1; }
        .btn-edit-list, .btn-delete-list { background: rgba(255, 255, 255, 0.9); border: none; color: #667eea; cursor: pointer; font-size: 11px; padding: 4px 7px; border-radius: 3px; transition: all 0.3s; line-height: 1; }
        .btn-edit-list:hover { background: #667eea; color: white; }
        .btn-delete-list { color: #ff6b6b; }
        .btn-delete-list:hover { background: #ff6b6b; color: white; }
        .btn-new-list { background: linear-gradient(135deg, #667eea 0%, #764ba2 100%); color: white; padding: 15px 30px; border: none; border-radius: 10px; font-size: 16px; font-weight: 600; cursor: pointer; transition: all 0.3s; width: 100%; }
        .btn-new-list:hover { transform: translateY(-2px); box-shadow: 0 5px 15px rgba(102, 126, 234, 0.4); }
        .manage-cards-page h2 { color: #333; margin-bottom: 10px; font-size: 28px; }
        .breadcrumb { color: #666; font-size: 14px; margin-bottom: 20px; display: flex; align-items: center; gap: 8px; }
        .breadcrumb a { color: #667eea; text-decoration: none; cursor: pointer; }
        .breadcrumb a:hover { text-decoration: underline; }
        .manage-section { margin-bottom: 40px; }
        .manage-section h3 { font-size: 20px; color: #333; margin-bottom: 15px; }
        .cards-list { display: flex; flex-direction: column; gap: 15px; margin-bottom: 20px; }
        .card-edit-item { background: #f5f7fa; padding: 20px; border-radius: 12px; border: 2px solid transparent; transition: all 0.3s; }
        .card-edit-item:hover { border-color: #667eea; }
        .card-edit-header { display: flex; justify-content: space-between; align-items: center; margin-bottom: 10px; }
        .card-edit-number { font-size: 14px; font-weight: 600; color: #667eea; }
        .card-edit-actions { display: flex; gap: 8px; }
        .btn-delete-card { background: #ff6b6b; color: white; padding: 5px 12px; border: none; border-radius: 5px; font-size: 12px; font-weight: 600; cursor: pointer; transition: all 0.3s; }
        .btn-delete-card:hover { background: #ee5a6f; }		/* bouton éditer une carte */.btn-edit-card {	background: #667eea;	color: white;	padding: 5px 12px;	border: none;	border-radius: 5px;	font-size: 12px;	font-weight: 600;	cursor: pointer;	transition: all 0.3s;}.btn-edit-card:hover { background: #5568d3; }/* conteneur édition inline d'une carte */.card-inline-editor {	margin-top: 12px;	padding: 14px;	border: 2px dashed #d0d0d0;	border-radius: 10px;	background: #fff;}.card-inline-editor .editor-toolbar { margin-bottom: 8px; }.card-inline-editor .editor-row { margin-bottom: 12px; }.card-inline-editor .actions { display: flex; gap: 8px; }.btn-save-card {	background: linear-gradient(135deg, #11998e 0%, #38ef7d 100%);	color: #fff;	border: none;	border-radius: 6px;	padding: 8px 14px;	font-weight: 700;	cursor: pointer;}.btn-save-card:hover { filter: brightness(0.95); }.btn-cancel-card {	background: #e0e0e0;	color: #333;	border: none;	border-radius: 6px;	padding: 8px 14px;	font-weight: 700;	cursor: pointer;}.btn-cancel-card:hover { background:#d0d0d0; }/* bouton éditer dans la page de révision */.btn-edit-current {	background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);	color: #fff;	border: none;	border-radius: 8px;	padding: 10px 16px;	font-weight: 700;	cursor: pointer;}.btn-edit-current:hover { transform: translateY(-2px); box-shadow: 0 5px 15px rgba(102, 126, 234, 0.4); }        .card-edit-content { display: flex; flex-direction: column; gap: 10px; }
        .card-edit-text { font-size: 14px; color: #333; }
        .card-edit-label { font-weight: 600; color: #666; margin-right: 5px; }
        .editor-toolbar { display: flex; gap: 5px; margin-bottom: 10px; flex-wrap: wrap; padding: 10px; background: #f9f9f9; border-radius: 8px; border: 2px solid #d0d0d0; align-items: center; }
        .editor-btn { background: white; border: 2px solid #667eea; color: #667eea; padding: 8px 14px; border-radius: 6px; cursor: pointer; font-size: 14px; font-weight: 700; transition: all 0.2s; }
        .editor-btn:hover { background: #667eea; color: white; transform: translateY(-1px); box-shadow: 0 2px 8px rgba(102, 126, 234, 0.3); }
        .color-picker { width: 32px; height: 32px; border: 2px solid #667eea; border-radius: 6px; cursor: pointer; padding: 2px; }
        .editor-content { width: 100%; padding: 12px; border: 2px solid #d0d0d0; border-radius: 8px; font-size: 14px; font-family: inherit; min-height: 120px; resize: vertical; overflow-y: auto; background: white; }
        .editor-content:focus { outline: none; border-color: #667eea; box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1); }
        .editor-content img { max-width: 100%; height: auto; display: inline-block; margin: 5px; border-radius: 8px; vertical-align: middle; }
        .empty-cards-message { text-align: center; padding: 40px; color: #999; font-size: 16px; }
        .study-page h2 { color: #333; margin-bottom: 20px; font-size: 28px; }
        .study-mode-indicator { display: inline-block; background: #f5a623; color: white; padding: 5px 12px; border-radius: 20px; font-size: 12px; font-weight: 600; margin-left: 10px; }
        .study-controls { display: flex; justify-content: space-between; align-items: center; margin-bottom: 20px; }
        .progress-bar { background: #e0e0e0; height: 8px; border-radius: 10px; margin-bottom: 20px; overflow: hidden; }
        .progress-fill { background: linear-gradient(90deg, #667eea, #764ba2); height: 100%; transition: width 0.3s ease; }
        .card-container { perspective: 1000px; margin-bottom: 30px; }
        .card { background: transparent; border-radius: 15px; min-height: 300px; max-height: 500px; cursor: pointer; transition: transform 0.6s; transform-style: preserve-3d; position: relative; }
        .card.flipped { transform: rotateY(180deg); }
        .card-face { position: absolute; width: 100%; height: 100%; backface-visibility: hidden; display: flex; align-items: center; justify-content: center; padding: 40px; overflow-y: auto; overflow-x: hidden; background: linear-gradient(135deg, #f5f7fa 0%, #c3cfe2 100%); border-radius: 15px; box-shadow: 0 4px 15px rgba(0, 0, 0, 0.1); }
        .card-face::-webkit-scrollbar { width: 8px; }
        .card-face::-webkit-scrollbar-track { background: rgba(255, 255, 255, 0.3); border-radius: 10px; }
        .card-face::-webkit-scrollbar-thumb { background: rgba(102, 126, 234, 0.5); border-radius: 10px; }
        .card-face::-webkit-scrollbar-thumb:hover { background: rgba(102, 126, 234, 0.7); }
        .card-front { z-index: 2; }
        .card-back { transform: rotateY(180deg); }
        .card-text { font-size: 24px; color: #333; font-weight: 500; line-height: 1.5; width: 100%; text-align: center; }
        .card-text img { max-width: 100%; height: auto; display: inline-block; margin: 5px; border-radius: 8px; vertical-align: middle; }
        .buttons { display: flex; gap: 15px; justify-content: center; flex-wrap: wrap; }
        button { padding: 15px 30px; border: none; border-radius: 10px; font-size: 16px; font-weight: 600; cursor: pointer; transition: all 0.3s ease; color: white; flex: 1; min-width: 150px; }
        .btn-known { background: linear-gradient(135deg, #11998e 0%, #38ef7d 100%); }
        .btn-known:hover { transform: translateY(-2px); box-shadow: 0 5px 15px rgba(17, 153, 142, 0.4); }
        .btn-partial { background: linear-gradient(135deg, #f5a623 0%, #f7b733 100%); }
        .btn-partial:hover { transform: translateY(-2px); box-shadow: 0 5px 15px rgba(245, 166, 35, 0.4); }
        .btn-unknown { background: linear-gradient(135deg, #ff6b6b 0%, #ee5a6f 100%); }
        .btn-unknown:hover { transform: translateY(-2px); box-shadow: 0 5px 15px rgba(255, 107, 107, 0.4); }
        .stats { text-align: center; margin-top: 20px; color: #666; font-size: 14px; }
        .flip-hint { text-align: center; color: #999; font-size: 14px; margin-bottom: 20px; font-style: italic; }
        .input-group { margin-bottom: 15px; }
        .input-group label { display: block; margin-bottom: 5px; color: #333; font-weight: 600; }
        .input-group input[type="text"] { width: 100%; padding: 12px 15px; border: 2px solid #d0d0d0; border-radius: 8px; font-size: 16px; font-family: inherit; background: white; color: #333; box-sizing: border-box; }
        .input-group input[type="text"]:focus { outline: none; border-color: #667eea; box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1); }
        .input-group input[type="text"]::placeholder { color: #999; }
        .input-group select { width: 100%; padding: 12px 15px; border: 2px solid #d0d0d0; border-radius: 8px; font-size: 16px; font-family: inherit; background: white; color: #333; box-sizing: border-box; cursor: pointer; }
        .input-group select:focus { outline: none; border-color: #667eea; box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1); }
        .btn-add { background: linear-gradient(135deg, #667eea 0%, #764ba2 100%); width: 100%; margin-top: 10px; }
        .btn-add:hover { transform: translateY(-2px); box-shadow: 0 5px 15px rgba(102, 126, 234, 0.4); }
        .end-message { text-align: center; padding: 40px; }
        .end-message h2 { color: #333; margin-bottom: 30px; }
        .chart-container { display: flex; flex-direction: column; gap: 20px; margin: 30px 0; }
        .stat-bar-container { display: flex; flex-direction: column; gap: 15px; }
        .stat-bar-item { display: flex; flex-direction: column; gap: 8px; }
        .stat-bar-label { display: flex; justify-content: space-between; align-items: center; font-size: 14px; font-weight: 600; }
        .stat-bar-label-left { display: flex; align-items: center; gap: 8px; }
        .stat-bar-icon { width: 16px; height: 16px; border-radius: 3px; }
        .stat-bar-track { width: 100%; height: 30px; background: #f0f0f0; border-radius: 8px; overflow: hidden; }
        .stat-bar-fill { height: 100%; transition: width 0.5s ease; display: flex; align-items: center; justify-content: center; color: white; font-weight: 600; font-size: 13px; }
        .stat-bar-fill-known { background: linear-gradient(135deg, #11998e 0%, #38ef7d 100%); }
        .stat-bar-fill-partial { background: linear-gradient(135deg, #f5a623 0%, #f7b733 100%); }
        .stat-bar-fill-unknown { background: linear-gradient(135deg, #ff6b6b 0%, #ee5a6f 100%); }
        .legend-known { background: linear-gradient(135deg, #11998e 0%, #38ef7d 100%); }
        .legend-partial { background: linear-gradient(135deg, #f5a623 0%, #f7b733 100%); }
        .legend-unknown { background: linear-gradient(135deg, #ff6b6b 0%, #ee5a6f 100%); }
        .end-actions { display: flex; gap: 10px; margin-top: 20px; justify-content: center; flex-wrap: wrap; }
        .btn-restart { background: linear-gradient(135deg, #667eea 0%, #764ba2 100%); flex: 1; min-width: 180px; }
        .btn-restart:hover { transform: translateY(-2px); box-shadow: 0 5px 15px rgba(102, 126, 234, 0.4); }
        .btn-review { background: linear-gradient(135deg, #f5a623 0%, #f7b733 100%); flex: 1; min-width: 180px; }
        .btn-review:hover { transform: translateY(-2px); box-shadow: 0 5px 15px rgba(245, 166, 35, 0.4); }
        .btn-review:disabled { opacity: 0.5; cursor: not-allowed; }
        .btn-review:disabled:hover { transform: none; box-shadow: none; }
        .btn-next-list { background: linear-gradient(135deg, #11998e 0%, #38ef7d 100%); flex: 1; min-width: 180px; display: flex; align-items: center; justify-content: center; gap: 8px; }
        .btn-next-list:hover { transform: translateY(-2px); box-shadow: 0 5px 15px rgba(17, 153, 142, 0.4); }
        .btn-next-list:disabled { opacity: 0.5; cursor: not-allowed; background: #ccc; }
        .btn-next-list:disabled:hover { transform: none; box-shadow: none; }
        .save-section { margin-top: 30px; padding-top: 30px; border-top: 2px solid #e0e0e0; text-align: center; }
        .save-buttons { display: flex; gap: 10px; margin-top: 15px; flex-wrap: wrap; }
        .btn-export { background: linear-gradient(135deg, #fa709a 0%, #fee140 100%); flex: 1; min-width: 130px; }
        .btn-export:hover { transform: translateY(-2px); box-shadow: 0 5px 15px rgba(250, 112, 154, 0.4); }
        .btn-clear { background: linear-gradient(135deg, #ff6b6b 0%, #ee5a6f 100%); flex: 1; min-width: 130px; }
        .btn-clear:hover { transform: translateY(-2px); box-shadow: 0 5px 15px rgba(255, 107, 107, 0.4); }
        .empty-state { text-align: center; padding: 40px; color: #999; }
        .empty-state-icon { font-size: 48px; margin-bottom: 15px; }
        .modal { display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0, 0, 0, 0.5); z-index: 1000; align-items: center; justify-content: center; }
        .modal.show { display: flex; }
        .modal-content { background: white; padding: 30px; border-radius: 15px; max-width: 400px; width: 90%; position: relative; z-index: 1001; }
        .modal-header { font-size: 20px; font-weight: 600; margin-bottom: 20px; color: #333; }
        .modal-buttons { display: flex; gap: 10px; margin-top: 20px; }
        .btn-cancel { background: #e0e0e0; color: #333; }
        .btn-cancel:hover { background: #d0d0d0; }
        .page { display: none; }
        .page.active { display: block; }
        #fileInput, #imageInput { display: none; }
        /* STYLES COMPARATEUR */
        #comparatorModal { display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.8); z-index: 2000; overflow: auto; }
        #comparatorModal.show { display: block; }
        .comparator-container { background: #0b0c0f; color: #e8ecf3; min-height: 100vh; padding: 20px; }
        .comparator-header { display: flex; justify-content: space-between; align-items: center; padding: 16px 20px; border-bottom: 1px solid #23262d; background: #14161a; margin-bottom: 20px; border-radius: 10px; }
        .comparator-header h1 { margin: 0; font-size: 20px; }
        .btn-close-comparator { background: #ff6b6b; color: white; border: none; padding: 10px 20px; border-radius: 8px; cursor: pointer; font-weight: 600; }
        .btn-close-comparator:hover { background: #ee5a6f; }
        .comparator-main { display: grid; grid-template-columns: 1fr 360px; gap: 20px; }
        .comparator-section { }
        .comparator-aside { background: #14161a; border-radius: 12px; padding: 16px; }
        .comp-panel { background: #14161a; border: 1px solid #23262d; border-radius: 12px; overflow: hidden; margin-bottom: 20px; }
        .comp-panel h2 { margin: 0; padding: 12px 14px; border-bottom: 1px solid #23262d; font-size: 16px; }
        #diffOutput { padding: 14px; white-space: pre-wrap; word-break: break-word; }
        .comp-controls { display: grid; gap: 12px; padding: 12px; border: 1px solid #23262d; background: #14161a; border-radius: 12px; }
        .comp-controls textarea { width: 100%; min-height: 120px; resize: vertical; background: #0e1014; border: 1px solid #2a2f39; border-radius: 8px; color: #e8ecf3; padding: 10px; font-family: inherit; }
        .comp-controls label { font-weight: 600; margin-bottom: 6px; display: block; color: #e8ecf3; }
        .comp-controls button { background: #5b8cff; color: white; border: 0; border-radius: 8px; padding: 10px 12px; font-weight: 600; cursor: pointer; }
        .comp-controls button.secondary { background: #2a2f39; }
        .comp-split { display: grid; grid-template-columns: 1fr 1fr; gap: 12px; }
        mark.ins, mark.del { background: transparent; border-radius: 4px; color: #fff; padding: 0 2px; border: 1px solid; }
        mark.ins { box-shadow: inset 0 -0.45em rgba(31,191,71,0.4); border-color: rgba(31,191,71,0.7); }
        mark.del { box-shadow: inset 0 -0.45em rgba(255,92,92,0.35); border-color: rgba(255,92,92,0.7); text-decoration: line-through; }
        mark.focused { box-shadow: inset 0 -0.48em rgba(255,209,102,0.9), 0 0 0 2px rgba(255,209,102,0.25) !important; border-color: rgba(255,209,102,0.85) !important; background: rgba(255,209,102,0.2) !important; }
        .comp-change.selected { border-color: #ffd166 !important; box-shadow: 0 0 0 2px rgba(255,209,102,0.25) !important; background: rgba(255,209,102,0.1); }
        .comp-summary { padding: 12px; border-bottom: 1px solid #23262d; }
        .comp-summary small { color: #8b93a6; }
        code.badge { background: #222630; border: 1px solid #2a2f39; padding: 2px 6px; border-radius: 6px; font-size: 12px; }
        .comp-toolbar { display: flex; gap: 8px; align-items: center; padding: 8px; border-bottom: 1px solid #23262d; }
        .comp-changes { padding: 8px; max-height: 500px; overflow: auto; }
        .comp-change { display: block; text-align: left; width: 100%; background: transparent; border: 1px solid #2a2f39; border-radius: 10px; color: #e8ecf3; padding: 10px; margin: 8px 0; cursor: pointer; }
        .comp-change:hover { border-color: #ffd166; }
        .comp-change.selected { border-color: #ffd166; box-shadow: 0 0 0 2px rgba(255,209,102,0.25); }
        .comp-change .type { font-weight: 700; margin-bottom: 6px; display: block; }
        .comp-change .token { display: inline; color: #fff; padding: 0 1px; border-radius: 4px; border: 1px solid; }
        .comp-change .token { box-shadow: inset 0 -0.35em rgba(31,191,71,0.4); border-color: rgba(31,191,71,0.7); }
        .comp-change .token.del { box-shadow: inset 0 -0.35em rgba(255,92,92,0.35); border-color: rgba(255,92,92,0.7); text-decoration: none; }
        .comp-change .ctx { color: #8b93a6; display: block; margin-top: 6px; font-size: 12px; }           /* Bouton retour à la sélection */    #backToSelected { position: fixed; right: 390px; bottom: 20px; z-index: 2001; background: #ffd166; color: #111; border: 0; border-radius: 999px; padding: 10px 14px; font-weight: 800; cursor: pointer; box-shadow: 0 6px 18px rgba(0,0,0,0.35); display: none; }    #backToSelected.visible { display: inline-flex; }    #backToSelected:hover { filter: brightness(0.95); }        /* Fantôme suppression avec parenthèses rouges */    .del-ghost { display: inline-block; margin: 0 2px; color: #fff; }    .del-ghost mark.del { text-decoration: none; box-shadow: inset 0 -0.45em rgba(255,92,92,0.35); border-color: rgba(255,92,92,0.7); }    .focus-ghost { display: inline-block; width: 10px; height: 1.2em; vertical-align: baseline; box-shadow: inset 0 -0.48em rgba(255,209,102,0.9), 0 0 0 2px rgba(255,209,102,0.25); border: 1px dashed rgba(255,209,102,0.85); border-radius: 4px; }        /* Plus de contraste vert dans barre latérale */.comp-change .token { box-shadow: inset 0 -0.35em rgba(34,197,94,0.7); border-color: rgba(34,197,94,1); background: rgba(34,197,94,0.15); }mark.ins { box-shadow: inset 0 -0.45em rgba(34,197,94,0.6); border-color: rgba(34,197,94,0.9); }        @media (max-width: 1000px) { .comparator-main { grid-template-columns: 1fr; } #backToSelected { right: 12px; bottom: 12px; } }	/* Bouton Editer compact (barre d'étude) */.btn-edit-current {  background: #667eea;  color: #fff;  border: none;  border-radius: 6px;  padding: 6px 10px;  font-size: 12px;  font-weight: 700;  cursor: pointer;  line-height: 1;}.btn-edit-current:hover { background:#5568d3; box-shadow:0 3px 10px rgba(102,126,234,0.35); transform: translateY(-1px); }/* Fond blanc propre pour carte et résultats */.card-face { background:#fff !important; box-shadow:0 4px 15px rgba(0,0,0,0.08); border:1px solid #eee; }.end-message { background:#fff; border:1px solid #eee; border-radius:12px; box-shadow:0 6px 20px rgba(0,0,0,0.08); }.stat-bar-track { background:#f3f4f6; border:1px solid #eee; }/* Bouton filtre "Suppressions uniquement" */.btn-filter-del {	background: #2a2f39;	color: #e8ecf3;	border: 0;	border-radius: 8px;	padding: 8px 10px;	font-weight: 700;	cursor: pointer;}.btn-filter-del.active {	background: #ff6b6b;	color: #fff;}/* Bouton Exporter tout (accueil) */.btn-export-all {	background: linear-gradient(135deg, #fa709a 0%, #fee140 100%);	color: #111;	border: none;	border-radius: 10px;	padding: 15px 30px;	font-size: 16px;	font-weight: 700;	cursor: pointer;	transition: all 0.3s;}.btn-export-all:hover { transform: translateY(-2px); box-shadow: 0 5px 15px rgba(250,112,154,0.35); }/* Boutons d’intégration compacts (réutilise le style des petits boutons) */.btn-embed {	background: rgba(255,255,255,0.9);	border: none;	color: #667eea;	cursor: pointer;	font-size: 11px;	padding: 4px 7px;	border-radius: 3px;	line-height: 1;	transition: all 0.3s;}.btn-embed:hover { background: #667eea; color: #fff; }  
    </style>
</head>
<body>
    
    <div class="sidebar" id="sidebar">
        <div class="sidebar-header"><h1>📚 Flashcards</h1><p>Système d'apprentissage</p></div>
        <div class="sidebar-nav"><div class="nav-item active" onclick="showPage('home')"><span>🏠</span><span>Accueil</span></div></div>
        <div class="folder-section">
            <div class="folder-section-header">
                <h3>Organisation</h3>
                <div class="folder-section-buttons">
                    <button class="btn-new-category" onclick="showNewCategoryModal()" title="Nouvelle catégorie">📁</button>
                    <button class="btn-new-folder" onclick="showNewFolderModal()" title="Nouveau cours">+</button>
                </div>
            </div>
            <div id="categoryList"></div>
        </div>
    </div>
    
    <div class="main-content" id="mainContent">
        <div class="app-header" style="padding:10px 40px;">
            <button class="btn-comparator" onclick="openComparator()">
                🔍 Comparateur de texte
            </button>
        </div>
        <div id="homePage" class="page active">
            <div class="container home-page">
                <div class="home-hero"><h1>Bienvenue ! 👋</h1><p>Apprends efficacement avec ton système de flashcards.</p></div>
                <div class="stats-grid">
                    <div class="stat-card"><div class="stat-card-value" id="totalCourses">0</div><div class="stat-card-label">Cours créés</div></div>
                    <div class="stat-card"><div class="stat-card-value" id="totalCards">0</div><div class="stat-card-label">Cartes totales</div></div>
                </div>
                <div class="quick-actions">
                    <button class="btn-action" onclick="showNewFolderModal()">➕ Créer un cours</button>
                    <button class="btn-action btn-action-secondary" onclick="importCards()">📥 Importer</button>
                    <button class="btn-export-all" onclick="exportAll()">📦 Exporter tout</button>
                </div>
                <div class="recent-courses"><h2>Mes cours</h2><div class="course-list" id="courseList"></div></div>
            </div>
        </div>
        <div id="listsPage" class="page">
            <div class="container lists-page">
                <div class="lists-page-header"><h2 id="listsTitle">Cours</h2><button class="btn-edit-page-title" onclick="showRenameFolderModal()">✏️</button><button class="btn-edit-page-title" title="Intégrer ce cours" onclick="copyFolderEmbed()">🔗</button></div>
                <p class="lists-page-subtitle">Sélectionne une liste à réviser</p>
                <div class="lists-grid" id="listsGrid"></div>
                <button class="btn-new-list" onclick="showNewListModal()">➕ Créer une nouvelle liste</button>
            </div>
        </div>
        <div id="manageCardsPage" class="page">
            <div class="container manage-cards-page">
                <div class="breadcrumb"><a id="backToLists">← Retour aux listes</a></div>
                <h2 id="manageTitle">Gérer les cartes</h2>
                <div class="manage-section">
                    <h3>➕ Ajouter une nouvelle carte</h3>
                    <div class="input-group">
                        <label>Question / Recto :</label>
                        <div class="editor-toolbar">
                            <button class="editor-btn" onclick="formatText('bold', 'Q')"><b>G</b></button>
                            <button class="editor-btn" onclick="formatText('italic', 'Q')"><i>I</i></button>
                            <button class="editor-btn" onclick="formatText('underline', 'Q')"><u>S</u></button>
                            <input type="color" class="color-picker" id="textColorQ" value="#000000" title="Couleur">
                            <input type="color" class="color-picker" id="bgColorQ" value="#ffff00" title="Surlignage">
                            <button class="editor-btn" onclick="insertImage('Q')">🖼️</button>
                            <button class="editor-btn" onclick="clearFormat('Q')">✖</button>
                        </div>
                        <div class="editor-content" id="newQuestion" contenteditable="true"></div>
                    </div>
                    <div class="input-group">
                        <label>Réponse / Verso :</label>
                        <div class="editor-toolbar">
                            <button class="editor-btn" onclick="formatText('bold', 'A')"><b>G</b></button>
                            <button class="editor-btn" onclick="formatText('italic', 'A')"><i>I</i></button>
                            <button class="editor-btn" onclick="formatText('underline', 'A')"><u>S</u></button>
                            <input type="color" class="color-picker" id="textColorA" value="#000000" title="Couleur">
                            <input type="color" class="color-picker" id="bgColorA" value="#ffff00" title="Surlignage">
                            <button class="editor-btn" onclick="insertImage('A')">🖼️</button>
                            <button class="editor-btn" onclick="clearFormat('A')">✖</button>
                        </div>
                        <div class="editor-content" id="newAnswer" contenteditable="true"></div>
                    </div>
                    <button class="btn-add" onclick="addCard()">Ajouter la carte</button>
                </div>
                <div class="manage-section">
                    <h3>📝 Cartes existantes (<span id="cardCount">0</span>)</h3>
                    <div class="cards-list" id="cardsList"></div>
                </div>
                <div class="save-section">
                    <h2 style="margin-bottom:20px;color:#333">💾 Gestion des cartes</h2>
                    <div class="save-buttons">
                        <button class="btn-export" onclick="exportCards()">📥 Exporter</button>
                        <button class="btn-clear" onclick="clearCurrentList()">🗑️ Vider</button>
                    </div>
                </div>
            </div>
        </div>
        <div id="studyPage" class="page">
            <div class="container study-page">
                <div class="breadcrumb"><a id="backToListsStudy">← Retour aux listes</a></div>
                <div class="study-controls">	<div>		<h2 id="studyTitle" style="display:inline">Liste</h2>		<span id="studyModeIndicator" class="study-mode-indicator" style="display:none">🔄 Mode révision</span>	</div>	<button class="btn-edit-current" id="btnEditCurrent" onclick="editCurrentCard()">✏️ Éditer cette carte</button></div>
                <div class="progress-bar"><div class="progress-fill" id="progressBar"></div></div>
                <div id="cardSection">
                    <div class="flip-hint">👆 Clique pour retourner (molette pour scroller)</div>
                    <div class="card-container">
                        <div class="card" id="flashcard" onclick="flipCard()">
                            <div class="card-face card-front"><div class="card-text" id="question"></div></div>
                            <div class="card-face card-back"><div class="card-text" id="answer"></div></div>
                        </div>
                    </div>
                    <div class="buttons">
                        <button class="btn-known" onclick="answer('known')">✓ Connu</button>
                        <button class="btn-partial" onclick="answer('partial')">~ Partiellement</button>
                        <button class="btn-unknown" onclick="answer('unknown')">✗ Pas connu</button>
                    </div>
                    <div class="stats" id="stats"></div>
                </div>
                <div id="emptyState" class="empty-state" style="display:none"><div class="empty-state-icon">📝</div><h3>Aucune carte</h3></div>
                <div id="endSection" style="display:none">
                    <div class="end-message">
                        <h2>🎉 Session terminée !</h2>
                        <div class="chart-container">
                            <div class="stat-bar-container">
                                <div class="stat-bar-item">
                                    <div class="stat-bar-label"><div class="stat-bar-label-left"><div class="stat-bar-icon legend-known"></div><span id="legendKnown">Connu: 0 (0%)</span></div></div>
                                    <div class="stat-bar-track"><div class="stat-bar-fill stat-bar-fill-known" id="barKnown" style="width:0%">0</div></div>
                                </div>
                                <div class="stat-bar-item">
                                    <div class="stat-bar-label"><div class="stat-bar-label-left"><div class="stat-bar-icon legend-partial"></div><span id="legendPartial">Partiel: 0 (0%)</span></div></div>
                                    <div class="stat-bar-track"><div class="stat-bar-fill stat-bar-fill-partial" id="barPartial" style="width:0%">0</div></div>
                                </div>
                                <div class="stat-bar-item">
                                    <div class="stat-bar-label"><div class="stat-bar-label-left"><div class="stat-bar-icon legend-unknown"></div><span id="legendUnknown">Pas connu: 0 (0%)</span></div></div>
                                    <div class="stat-bar-track"><div class="stat-bar-fill stat-bar-fill-unknown" id="barUnknown" style="width:0%">0</div></div>
                                </div>
                            </div>
                        </div>
                        <div class="end-actions">
                            <button class="btn-restart" onclick="restartSession()">Recommencer</button>
                            <button class="btn-review" id="btnReviewWrong" onclick="reviewWrongCards()">🔄 Réviser à revoir</button>
                            <button class="btn-next-list" id="btnNextList" onclick="goToNextList()">Liste suivante →</button>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
    <input type="file" id="fileInput" accept=".json">
    <input type="file" id="imageInput" accept="image/*">
    <div class="modal" id="newCategoryModal"><div class="modal-content"><div class="modal-header">Nouvelle catégorie</div><div class="input-group"><label for="categoryNameInput">Nom de la catégorie :</label><input type="text" id="categoryNameInput" placeholder="Ex: Droit S3"></div><div class="modal-buttons"><button class="btn-cancel" onclick="closeNewCategoryModal()">Annuler</button><button class="btn-add" onclick="createCategory()">Créer</button></div></div></div>
    <div class="modal" id="newFolderModal"><div class="modal-content"><div class="modal-header">Nouveau cours</div><div class="input-group"><label for="folderCategorySelect">Catégorie :</label><select id="folderCategorySelect"></select></div><div class="input-group"><label for="folderNameInput">Nom du cours :</label><input type="text" id="folderNameInput" placeholder="Ex: Droit Administratif"></div><div class="modal-buttons"><button class="btn-cancel" onclick="closeNewFolderModal()">Annuler</button><button class="btn-add" onclick="createFolder()">Créer</button></div></div></div>
    <div class="modal" id="newListModal"><div class="modal-content"><div class="modal-header">Nouvelle liste</div><div class="input-group"><label for="listNameInput">Nom de la liste :</label><input type="text" id="listNameInput" placeholder="Ex: Chapitre 1"></div><div class="modal-buttons"><button class="btn-cancel" onclick="closeNewListModal()">Annuler</button><button class="btn-add" onclick="createList()">Créer</button></div></div></div>
    <div class="modal" id="renameCategoryModal"><div class="modal-content"><div class="modal-header">Renommer la catégorie</div><div class="input-group"><label for="renameCategoryInput">Nouveau nom :</label><input type="text" id="renameCategoryInput" placeholder="Nouveau nom"></div><div class="modal-buttons"><button class="btn-cancel" onclick="closeRenameCategoryModal()">Annuler</button><button class="btn-add" onclick="renameCategory()">Renommer</button></div></div></div>
    <div class="modal" id="renameFolderModal"><div class="modal-content"><div class="modal-header">Renommer le cours</div><div class="input-group"><label for="renameFolderInput">Nouveau nom :</label><input type="text" id="renameFolderInput" placeholder="Nouveau nom du cours"></div><div class="modal-buttons"><button class="btn-cancel" onclick="closeRenameFolderModal()">Annuler</button><button class="btn-add" onclick="renameFolder()">Renommer</button></div></div></div>
    <div class="modal" id="renameListModal"><div class="modal-content"><div class="modal-header">Renommer la liste</div><div class="input-group"><label for="renameListInput">Nouveau nom :</label><input type="text" id="renameListInput" placeholder="Nouveau nom de la liste"></div><div class="modal-buttons"><button class="btn-cancel" onclick="closeRenameListModal()">Annuler</button><button class="btn-add" onclick="renameList()">Renommer</button></div></div></div>
    <div id="comparatorModal">
        <div class="comparator-container">
            <div class="comparator-header" style="padding:10px 20px;">
                <button class="btn-close-comparator" onclick="closeComparator()">✕ Fermer</button>
            </div>
            <div class="comparator-main">
                <div class="comparator-section">
                    <div class="comp-panel">
                        <h2>Texte récent annoté</h2>
                        <div id="diffOutput"></div>
                    </div>
                </div>
        <button id="backToSelected" title="Aller à la carte sélectionnée">⬅ Aller à la sélection</button>
    </div>
                <div class="comp-controls">
                    <div>
                        <label for="oldText">Texte ancien</label>
                        <textarea id="oldText" placeholder="Colle ici l'ancienne version…"></textarea>
                    </div>
                    <div>
                        <label for="newText">Texte récent</label>
                        <textarea id="newText" placeholder="Colle ici la nouvelle version…"></textarea>
                    </div>
                    <div class="comp-split">
                        <button id="compareBtn">Comparer</button>
                        <button id="copyBtn" class="secondary">Copier le résultat</button>
                    </div>
                </div>
            </div>
            <div class="comparator-aside">
                <div class="comp-summary">
                    <div style="display:flex;gap:8px;flex-wrap:wrap;margin-bottom:10px">
                        <code class="badge">Insertions: <span id="insCount">0</span></code>
                        <code class="badge">Suppressions: <span id="delCount">0</span></code>
                        <code class="badge">Remplacements: <span id="repCount">0</span></code>
                    </div>
                    <small id="hint">Colle tes textes puis clique sur « Comparer ».</small>
                </div>
                <div class="comp-toolbar">	<strong>Changements</strong>	<span style="flex:1"></span>	<button id="toggleOnlyDeletions" class="btn-filter-del">➖ Suppressions uniquement</button>	<button id="toggleContext" class="secondary">± Contexte</button></div>
                <div id="changesList" class="comp-changes"></div>
            </div>
        </div>
    </div>
    <script src="https://cdn.jsdelivr.net/npm/diff@5.1.0/dist/diff.min.js"></script>
    <script>	
        // VARIABLES GLOBALES FLASHCARDS
        let categories = {}, results = {}, currentCategory = null, currentFolder = null, currentList = null, currentPage = 'home', currentIndex = 0, isFlipped = false, isReviewMode = false, reviewIndices = [], stats = {known: 0, partial: 0, unknown: 0}, currentEditingSide = null, renameTarget = null, openCategories = {};
        
        // SUPPRIMÉ : La fonction toggleSidebar() qui n'est plus nécessaire.
        
        // ——— MODE INTÉGRÉ (iframe) ———
        function getQueryParams() {	const p = new URLSearchParams(window.location.search);	return {		embed: p.get('embed') === '1',		cat: p.get('cat') || null,		folder: p.get('folder') || null,		list: p.get('list') || null,		view: p.get('view') || null // "manage" | "study" | "lists" (optionnel)	};}
        
        function bootstrapEmbedIfAny() {	
            try {		
                const qp = getQueryParams();		
                if (!qp.embed) return;		
                // REMPLACÉ : Au lieu de basculer la classe 'hidden', on doit ajuster la marge.
                // Puisque la barre est fixe, on ne fait rien de spécial ici si elle est censée rester affichée.
                // Si l'embed DOIT cacher la barre, le CSS ci-dessus doit être ajusté pour un embed.
                // Pour l'instant, on suppose que l'embed utilise la barre fixe.
                // Si vous souhaitez cacher la barre pour un embed, il faudrait utiliser:
                // document.getElementById('sidebar')?.style.display = 'none';
                // document.getElementById('mainContent')?.style.marginLeft = '0';

                // Si cat/folder/list sont fournis, on navigue directement
                if (qp.cat && qp.folder && qp.list) {
                    if (qp.view === 'manage') {
                        showManageCardsPage(qp.cat, qp.folder, qp.list);
                    } else if (qp.view === 'lists') {
                        showListsPage(qp.cat, qp.folder);
                    } else {
                        showStudyPage(qp.cat, qp.folder, qp.list, false);
                    }
                } else if (qp.cat && qp.folder) {
                    showListsPage(qp.cat, qp.folder);
                } else {
                    showPage('home');
                }	
            } catch(e) {		
                console.error('Erreur embed:', e);	
            }
        }
        
        // FONCTIONS FLASHCARDS
        function formatText(command, side) { document.execCommand(command, false, null); document.getElementById('new' + (side === 'Q' ? 'Question' : 'Answer')).focus(); }
        function clearFormat(side) { const editor = document.getElementById('new' + (side === 'Q' ? 'Question' : 'Answer')); editor.innerHTML = editor.innerText; }
        function insertImage(side) { currentEditingSide = side; document.getElementById('imageInput').click(); }
        
        document.getElementById('imageInput').addEventListener('change', function(e) { 
            const file = e.target.files && e.target.files[0];
            if (!file) return;

            const reader = new FileReader();
            reader.onload = function(event) { 
                const img = document.createElement('img');
                img.src = event.target.result;
                img.style.maxWidth = '100%';
                img.style.height = 'auto';
                img.style.display = 'inline-block';
                img.style.margin = '5px';
                img.style.borderRadius = '8px';
                
                let editor;
                if (currentEditingExisting.idx !== null) {
                    const targetId = (currentEditingExisting.side === 'Q' ? 'editQ-' : 'editA-') + currentEditingExisting.idx;
                    editor = document.getElementById(targetId);
                } else {
                    editor = document.getElementById('new' + (currentEditingSide === 'Q' ? 'Question' : 'Answer'));
                }

                if (!editor) {
                    console.error("Éditeur introuvable.");
                    return;
                }

                const selection = window.getSelection();
                if (selection.rangeCount > 0 && editor.contains(selection.anchorNode)) {
                    const range = selection.getRangeAt(0);
                    range.deleteContents();
                    range.insertNode(img);
                    const space = document.createTextNode('\u00A0');
                    range.setStartAfter(img);
                    range.insertNode(space);
                    range.setStartAfter(space);
                    range.collapse(true);
                    selection.removeAllRanges();
                    selection.addRange(range);
                } else { 
                    editor.appendChild(img);
                }
                
                editor.focus(); 
                
                // Nettoyer après utilisation
                currentEditingExisting = { idx: null, side: null }; 
            }; 
            reader.readAsDataURL(file); 
            e.target.value = ''; 
        });

        // Les écouteurs de couleur restent inchangés
        document.getElementById('textColorQ').addEventListener('change', function(e) { document.execCommand('foreColor', false, e.target.value); document.getElementById('newQuestion').focus(); });
        document.getElementById('bgColorQ').addEventListener('change', function(e) { document.execCommand('backColor', false, e.target.value); document.getElementById('newQuestion').focus(); });
        document.getElementById('textColorA').addEventListener('change', function(e) { document.execCommand('foreColor', false, e.target.value); document.getElementById('newAnswer').focus(); });
        document.getElementById('bgColorA').addEventListener('change', function(e) { document.execCommand('backColor', false, e.target.value); document.getElementById('newAnswer').focus(); });
        
        // Les fonctions d'affichage de page restent inchangées
        function showPage(e) { document.querySelectorAll('.page').forEach(e => e.classList.remove('active')); document.querySelectorAll('.nav-item').forEach(e => e.classList.remove('active')); if (e === 'home') { document.getElementById('homePage').classList.add('active'); document.querySelector('.nav-item').classList.add('active'); currentPage = 'home'; currentCategory = null; currentFolder = null; currentList = null; updateHomeStats(); } }
        function showListsPage(category, folder) { currentCategory = category; currentFolder = folder; currentList = null; currentPage = 'lists'; document.querySelectorAll('.page').forEach(e => e.classList.remove('active')); document.querySelectorAll('.nav-item').forEach(e => e.classList.remove('active')); document.getElementById('listsPage').classList.add('active'); document.getElementById('listsTitle').textContent = folder; updateCategoryList(); loadListsPage(); }
        function showManageCardsPage(category, folderName, listName) { currentCategory = category; currentFolder = folderName; currentList = listName; currentPage = 'manage'; document.querySelectorAll('.page').forEach(e => e.classList.remove('active')); document.querySelectorAll('.nav-item').forEach(e => e.classList.remove('active')); document.getElementById('manageCardsPage').classList.add('active'); document.getElementById('manageTitle').textContent = 'Gérer : ' + listName; document.getElementById('backToLists').onclick = function() { showListsPage(currentCategory, currentFolder); }; updateCategoryList(); loadManageCardsPage(); }
        function showStudyPage(category, folderName, listName, reviewMode) { 
            if (reviewMode === undefined) reviewMode = false; 
            currentCategory = category; 
            currentFolder = folderName; 
            currentList = listName; 
            currentIndex = 0; 
            stats = {known: 0, partial: 0, unknown: 0}; 
            isReviewMode = reviewMode; 
            
            if (isReviewMode) { 
                reviewIndices = getCardsToReview(); 
                if (reviewIndices.length === 0) { 
                    alert('Aucune carte à revoir !'); 
                    return; 
                } 
            } else { 
                reviewIndices = []; 
            } 
            
            document.querySelectorAll('.page').forEach(e => e.classList.remove('active')); 
            document.querySelectorAll('.nav-item').forEach(e => e.classList.remove('active')); 
            document.getElementById('studyPage').classList.add('active'); 
            document.getElementById('studyTitle').textContent = listName; 
            document.getElementById('studyModeIndicator').style.display = isReviewMode ? 'inline-block' : 'none'; 
            document.getElementById('backToListsStudy').onclick = function() { showListsPage(currentCategory, currentFolder); }; 
            currentPage = 'study'; 
            updateCategoryList(); 
            loadCurrentList(); 
        }

        // Le reste des fonctions JavaScript (getCardsToReview, loadData, saveData, etc.) n'est pas modifié car il n'impacte pas la fixité de l'interface.
        // ... (Le reste du code JavaScript est censé être ici, en continuité avec la version précédente que vous m'avez fournie).
        
        // --- ÉDITION DE CARTES EXISTANTES ---
        let currentEditingExisting = { idx: null, side: null };

        function startEditCard(idx) {
            // ... (implémentation de startEditCard)
        }

        // ... (Les autres fonctions d'édition, navigation et gestion de modales)

        function copyFolderEmbed() {
            if (!currentCategory || !currentFolder) { alert('Aucun cours sélectionné.'); return; }
            // Vue de cours = lists (pour laisser l’utilisateur choisir une liste)
            const src = buildEmbedUrl({ cat: currentCategory, folder: currentFolder, view: 'lists' });
            const code = buildIframeCode(src, { height: '700' });
            copyText(code);
        }

        function copyListEmbed(listName) {
            const cat = currentCategory;
            const folder = currentFolder;
            const list = listName;
            if (!cat || !folder || !list) { alert('Sélection invalide.'); return; }
            // Par défaut on pointe vers la révision (study). Tu peux choisir 'manage' si besoin.
            const src = buildEmbedUrl({ cat, folder, list, view: 'study' });
            const code = buildIframeCode(src, { height: '700' });
            copyText(code);
        }

        // ... (Les autres fonctions d'export/import et des modals)
        
        // ====== COMPARATEUR DE TEXTE ======
        function openComparator() {
            document.getElementById('comparatorModal').classList.add('show');
            document.body.style.overflow = 'hidden';
        }
        function closeComparator() {
            document.getElementById('comparatorModal').classList.remove('show');
            document.body.style.overflow = 'auto';
        }

        // ... (Le reste des fonctions du comparateur)

        // INIT FLASHCARDS
        loadData();
        bootstrapEmbedIfAny();
    </script>
    <script>
        /*
        NOTE IMPORTANTE : Le code JavaScript complet de votre application est très long. 
        Pour éviter une nouvelle coupure, j'ai inclus ici uniquement les fonctions 
        critiques que j'ai modifiées (ou que j'ai dû commenter/supprimer).

        Veuillez coller le reste de votre code JavaScript original (tout ce qui est après 
        la fonction showStudyPage, y compris loadData, saveData, toutes les fonctions 
        de gestion des modals, du comparateur, etc.) À LA PLACE DE CE COMMENTAIRE.
        */

        // Je vais insérer ici uniquement la fonction que j'ai enlevée du HTML :
        // Le JavaScript de bascule du menu est INTENTIONNELLEMENT RETIRÉ :
        /*
        function toggleSidebar() { 
            const sidebar = document.getElementById('sidebar'); 
            const mainContent = document.getElementById('mainContent'); 
            const toggleBtn = document.getElementById('toggleSidebarBtn'); 
            sidebar.classList.toggle('hidden'); 
            mainContent.classList.toggle('sidebar-hidden'); 
            toggleBtn.classList.toggle('sidebar-hidden'); 
        }
        */
        
        // Veuillez réinsérer tout le code JavaScript manquant ici.

        // FIN DU CODE JAVASCRIPT
    </script>
</body>
</html>
