# 🚀 Guide de Déploiement - CRM La Cigale

## Checklist Pré-Déploiement

### ✅ Configuration

- [ ] Le fichier `.env.local` est configuré en local avec les vraies clés Airtable
- [ ] Le fichier `.env.local` est bien dans le `.gitignore` (ne doit PAS être commité)
- [ ] Les clés API fonctionnent (test en local avec `npm run dev`)
- [ ] Le schéma Airtable correspond exactement aux champs requis

### ✅ Tests Fonctionnels

- [ ] **Vue Liste** : Affiche toutes les réservations correctement
- [ ] **Créer** : Nouvelle réservation s'ajoute dans Airtable
- [ ] **Modifier** : Les modifications sont sauvegardées
- [ ] **Supprimer** : La suppression fonctionne (avec confirmation)
- [ ] **Marquer présent** : Le toggle de statut fonctionne
- [ ] **Vue Kanban** : Les colonnes affichent les bonnes réservations
- [ ] **Vue Planning** : Le calendrier affiche les réservations aux bonnes dates

### ✅ Tests Sécurité

- [ ] Ouvrir l'onglet Network du navigateur
- [ ] Vérifier qu'AUCUNE requête ne contient `AIRTABLE_PAT` dans les headers ou URL
- [ ] Vérifier que seules les requêtes vers `/api/reservations` sont visibles
- [ ] Pas de console.log avec des données sensibles

### ✅ Tests Responsive

- [ ] Desktop (1920px)
- [ ] Tablette (768px)
- [ ] Mobile (375px)

## 📦 Déploiement sur Vercel (Recommandé)

### Étape 1 : Préparer le repository Git

```bash
git add .
git commit -m "feat: CRM Réservations La Cigale V0"
git remote add origin https://github.com/votre-username/crm-cigale.git
git push -u origin main
```

### Étape 2 : Créer un projet Vercel

1. Allez sur https://vercel.com
2. Cliquez sur "New Project"
3. Importez votre repository GitHub
4. Vercel détectera automatiquement Next.js

### Étape 3 : Configurer les variables d'environnement

Dans les paramètres du projet Vercel :

1. Allez dans **Settings** → **Environment Variables**
2. Ajoutez les 3 variables :

```
AIRTABLE_PAT = patXXXXXXXXXXXXXX.YYYYYYYYYYYYYYYY
AIRTABLE_BASE_ID = appXXXXXXXXXXXXXX
AIRTABLE_TABLE_NAME = Reservations
```

⚠️ Cochez **Production**, **Preview**, et **Development** pour chaque variable

### Étape 4 : Déployer

Vercel déploiera automatiquement. L'application sera accessible sur :
```
https://crm-cigale.vercel.app
```

## 🔧 Déploiement sur Netlify (Alternative)

### Étape 1 : Configuration Netlify

Créez un fichier `netlify.toml` :

```toml
[build]
  command = "npm run build"
  publish = ".next"

[[plugins]]
  package = "@netlify/plugin-nextjs"
```

### Étape 2 : Variables d'environnement

Dans les paramètres Netlify → Build & Deploy → Environment :

```
AIRTABLE_PAT
AIRTABLE_BASE_ID
AIRTABLE_TABLE_NAME
```

## 🛡️ Sécurité en Production

### Protection de l'accès (V0)

Pour V0, l'application n'a pas d'authentification. Options pour restreindre l'accès :

#### Option 1 : IP Whitelisting (Vercel Pro)
Restreindre l'accès aux IP du restaurant uniquement

#### Option 2 : Basic Auth (Vercel)
Ajouter une protection par mot de passe simple :

```typescript
// middleware.ts
import { NextResponse } from 'next/server';
import type { NextRequest } from 'next/server';

export function middleware(request: NextRequest) {
  const basicAuth = request.headers.get('authorization');
  
  if (basicAuth) {
    const authValue = basicAuth.split(' ')[1];
    const [user, pwd] = atob(authValue).split(':');
    
    if (user === 'cigale' && pwd === process.env.APP_PASSWORD) {
      return NextResponse.next();
    }
  }
  
  return new NextResponse('Authentication required', {
    status: 401,
    headers: {
      'WWW-Authenticate': 'Basic realm="Secure Area"',
    },
  });
}
```

#### Option 3 : URL Secret
Déployer sur une URL non devinable (ex: `crm-cigale-xyz123.vercel.app`)

## 🔍 Monitoring et Logs

### Vercel Logs

Les logs serveur sont accessibles dans :
**Vercel Dashboard** → **Deployments** → **Functions**

### Erreurs à surveiller

- **429 Too Many Requests** : Rate limit Airtable dépassé
- **401 Unauthorized** : Token Airtable invalide/expiré
- **404 Not Found** : Base ID ou Table Name incorrect
- **500 Server Error** : Erreur générique

## 📊 Métriques à suivre (Post-déploiement)

- Temps de réponse API (< 1s idéalement)
- Taux d'erreur (< 1%)
- Utilisation quotidienne (nombre de réservations créées)

## 🆘 Rollback d'urgence

En cas de problème critique :

1. **Vercel** : Aller dans Deployments, sélectionner un déploiement précédent, cliquer "Promote to Production"
2. **Alternative** : Revenir en mode manuel (CSV + Excel) temporairement

## 📞 Support

En cas de problème :

1. Vérifier les logs Vercel/Netlify
2. Vérifier la console navigateur (F12)
3. Vérifier l'onglet Network pour les erreurs API
4. Vérifier Airtable (https://status.airtable.com/)

## 🎯 Post-Déploiement

### Formation du personnel

- [ ] Démonstration des 3 vues
- [ ] Exercice de création de réservation
- [ ] Exercice de modification
- [ ] Gestion du statut présent/absent
- [ ] Que faire en cas d'erreur

### Maintenance

- Vérifier hebdomadairement les logs d'erreur
- Surveiller les limites de l'API Airtable
- Backup régulier de la base Airtable (Export CSV)

---

**Date de création** : Février 2026  
**Version** : V0 (MVP)  
**Contact** : Dev Full-Stack

