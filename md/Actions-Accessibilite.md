# Actions pour l'accessibilité - Conformité RGAA/OPQUAST

Ce document présente l'état actuel de l'accessibilité de l'application Penpal AI et définit un plan d'actions pour améliorer la conformité aux référentiels RGAA (Référentiel Général d'Amélioration de l'Accessibilité) et OPQUAST (qualité web).

## État actuel de l'accessibilité

### Analyse de l'existant

L'application Penpal AI intègre déjà plusieurs bonnes pratiques d'accessibilité :

#### ✅ Points positifs identifiés

**1. Structure sémantique HTML**
```84:90:penpal-frontend/src/pages/index.tsx
            <h1 className="text-5xl md:text-7xl font-extrabold mb-6 leading-tight">
              <span className="bg-gradient-to-r from-blue-600 via-purple-600 to-pink-600 bg-clip-text text-transparent">
                {t('home.hero.mainTitle')}
              </span>
              <br />
              <span className="text-gray-900 dark:text-white">{t('home.hero.mainSubtitle')}</span>
            </h1>
```

**2. Navigation sémantique avec landmarks**
```30:42:penpal-frontend/src/components/Layout.tsx
      <header className="border-b border-neutral-200 dark:border-neutral-800">
        <div className="container py-4 flex justify-between items-center">
          <div className="flex items-center">
            <Link
              href="/"
              className="text-2xl font-bold text-primary-600 dark:text-primary-400 mr-8"
            >
              PenPal AI
            </Link>

            {/* Navigation principale - visible seulement si authentifié */}
            {isAuthenticated && (
              <nav className="hidden md:flex space-x-6">
```

**3. Attributs ARIA implémentés**
```75:76:penpal-frontend/src/components/chat/MessageInput.tsx
          aria-label={t('input.attach')}
          title={t('input.attach')}
```

```122:124:penpal-frontend/src/components/ui/Modal/Modal.tsx
            role="dialog"
            aria-modal="true"
            aria-labelledby={title ? 'modal-title' : undefined}
```

**4. Support clavier**
```32:48:penpal-frontend/src/components/chat/AISettings.tsx
  // Close on escape key
  React.useEffect(() => {
    const handleEscape = (e: KeyboardEvent) => {
      if (e.key === 'Escape') {
        setIsOpen(false);
      }
    };

    if (isOpen) {
      document.addEventListener('keydown', handleEscape);
      document.body.style.overflow = 'hidden'; // Prevent background scroll
    }

    return () => {
      document.removeEventListener('keydown', handleEscape);
      document.body.style.overflow = 'unset';
    };
  }, [isOpen]);
```

**5. Internationalization (i18n)**
- Interface multilingue français/anglais
- Textes d'aide et labels traduits
- Support des langues d'apprentissage multiples

**6. Design adaptatif et contraste**
```12:14:penpal-frontend/src/styles/globals.css
  body {
    @apply bg-white text-neutral-900 dark:bg-neutral-900 dark:text-neutral-100 font-sans;
  }
```

**7. Mode sombre/clair**
- Thème adaptatif selon préférences système
- Contraste optimisé pour les deux modes
- Transition fluide entre thèmes

#### ⚠️ Points d'amélioration identifiés

**1. Navigation au clavier incomplète**
- Certains éléments interactifs manquent de focus visible
- Ordre de tabulation à optimiser
- Raccourcis clavier absents

**2. Attributs ARIA manquants**
- Descriptions étendues (`aria-describedby`)
- États dynamiques (`aria-expanded`, `aria-selected`)
- Régions live pour les mises à jour temps réel

**3. Alternatives textuelles**
- Images décoratives sans `alt=""`
- Icônes sans labels explicites
- Graphiques sans descriptions

**4. Gestion des erreurs**
- Messages d'erreur pas toujours associés aux champs
- Validation en temps réel sans annonce
- Instructions d'aide insuffisantes

## Plan d'actions RGAA 4.1

### Critère 1 : Images

#### Actions prioritaires

**1.1 - Alternatives textuelles**
```typescript
// ❌ Actuel
<img src="/logo.png" />

// ✅ À implémenter
<img src="/logo.png" alt="Penpal AI - Assistant d'apprentissage linguistique" />

// ✅ Pour images décoratives
<img src="/decoration.svg" alt="" role="presentation" />
```

**1.2 - Images porteuses d'information**
```typescript
// ✅ À implémenter pour les graphiques
<img 
  src="/chart.png" 
  alt="Graphique des progrès : 80% d'amélioration en compréhension orale sur 3 mois"
  longdesc="#chart-description"
/>
<div id="chart-description">
  <h3>Description détaillée du graphique</h3>
  <p>Ce graphique présente l'évolution...</p>
</div>
```

### Critère 2 : Cadres

**2.1 - Titre des cadres**
```typescript
// ✅ Déjà implémenté
<iframe
  title={`Grafana Dashboard: ${title}`}
  src={dashboardUrl}
/>
```

### Critère 3 : Couleurs

#### Actions prioritaires

**3.1 - Contraste des couleurs**
```typescript
// ✅ Configuration Tailwind à valider
const contrastRatios = {
  'text-neutral-900': '21:1', // Excellent
  'text-blue-600': '4.5:1',   // Conforme AA
  'text-gray-500': '3.2:1',   // ⚠️ À améliorer pour AAA
};
```

**3.2 - Information par la couleur**
```typescript
// ❌ Actuel : Information uniquement par couleur
<span className="text-red-500">Erreur</span>

// ✅ À implémenter : Information + icône + texte
<span className="text-red-500 flex items-center">
  <AlertCircle className="w-4 h-4 mr-1" />
  Erreur : Champ obligatoire
</span>
```

### Critère 4 : Multimédia

**4.1 - Médias temporels**
```typescript
// ✅ À implémenter pour les vidéos
<video controls>
  <source src="demo.mp4" type="video/mp4" />
  <track 
    kind="subtitles" 
    src="demo-fr.vtt" 
    srclang="fr" 
    label="Français"
  />
  <track 
    kind="subtitles" 
    src="demo-en.vtt" 
    srclang="en" 
    label="English"
  />
</video>
```

### Critère 6 : Liens

#### Actions prioritaires

**6.1 - Intitulés explicites**
```typescript
// ❌ Actuel : Lien générique
<Link href="/pricing">En savoir plus</Link>

// ✅ À implémenter
<Link href="/pricing">
  En savoir plus sur les tarifs Penpal AI
  <span className="sr-only"> (ouvre dans la même fenêtre)</span>
</Link>
```

### Critère 7 : Scripts

**7.1 - Fonctionnalités JavaScript accessibles**
```typescript
// ✅ Composant modal accessible
export function AccessibleModal({ title, children, onClose }: ModalProps) {
  const [isOpen, setIsOpen] = useState(false);
  
  // Gestion focus
  useEffect(() => {
    if (isOpen) {
      const firstFocusable = modalRef.current?.querySelector(
        'button, [href], input, select, textarea, [tabindex]:not([tabindex="-1"])'
      );
      firstFocusable?.focus();
    }
  }, [isOpen]);

  return (
    <div
      role="dialog"
      aria-modal="true"
      aria-labelledby="modal-title"
      aria-describedby="modal-description"
    >
      <h2 id="modal-title">{title}</h2>
      <div id="modal-description">{children}</div>
    </div>
  );
}
```

### Critère 10 : Présentation de l'information

**10.1 - Gestion du CSS désactivé**
```css
/* ✅ CSS à implémenter pour fallback */
.visually-hidden {
  position: absolute !important;
  width: 1px !important;
  height: 1px !important;
  padding: 0 !important;
  margin: -1px !important;
  overflow: hidden !important;
  clip: rect(0, 0, 0, 0) !important;
  white-space: nowrap !important;
  border: 0 !important;
}
```

### Critère 11 : Formulaires

#### Actions prioritaires

**11.1 - Étiquetage des champs**
```typescript
// ✅ Partiellement implémenté
<FormField
  label="Email"
  name="email"
  type="email"
  required
  aria-describedby="email-help"
/>
<div id="email-help" className="text-sm text-gray-600">
  Votre adresse email sera utilisée pour la connexion
</div>
```

**11.2 - Regroupement de champs**
```typescript
// ✅ À implémenter
<fieldset>
  <legend>Informations personnelles</legend>
  <FormField label="Prénom" name="firstName" />
  <FormField label="Nom" name="lastName" />
</fieldset>

<fieldset>
  <legend>Préférences d'apprentissage</legend>
  <FormField label="Langue" name="language" type="select" />
  <FormField label="Niveau" name="level" type="select" />
</fieldset>
```

**11.3 - Messages d'erreur**
```typescript
// ✅ À améliorer
export function AccessibleFormField({ name, error, ...props }: FormFieldProps) {
  const errorId = `${name}-error`;
  
  return (
    <div>
      <Input
        {...props}
        aria-invalid={error ? 'true' : 'false'}
        aria-describedby={error ? errorId : undefined}
      />
      {error && (
        <div 
          id={errorId} 
          role="alert" 
          className="text-red-600 text-sm mt-1"
        >
          <AlertCircle className="w-4 h-4 inline mr-1" />
          {error}
        </div>
      )}
    </div>
  );
}
```

### Critère 12 : Navigation

#### Actions prioritaires

**12.1 - Plan du site et navigation**
```typescript
// ✅ À implémenter
export function BreadcrumbNavigation({ items }: BreadcrumbProps) {
  return (
    <nav aria-label="Fil d'Ariane">
      <ol className="flex space-x-2">
        {items.map((item, index) => (
          <li key={index}>
            {index < items.length - 1 ? (
              <>
                <Link href={item.href}>{item.label}</Link>
                <span aria-hidden="true" className="mx-2">/</span>
              </>
            ) : (
              <span aria-current="page">{item.label}</span>
            )}
          </li>
        ))}
      </ol>
    </nav>
  );
}
```

**12.2 - Navigation principale**
```typescript
// ✅ Partiellement implémenté, à améliorer
<nav aria-label="Navigation principale">
  <ul className="flex space-x-6">
    <li>
      <NavLink 
        href="/chat" 
        active={router.pathname === '/chat'}
        aria-current={router.pathname === '/chat' ? 'page' : undefined}
      >
        Chat
      </NavLink>
    </li>
  </ul>
</nav>
```

### Critère 13 : Consultation

**13.1 - Limite de temps**
```typescript
// ✅ À implémenter pour les sessions
export function SessionTimeout() {
  const [timeLeft, setTimeLeft] = useState(600); // 10 minutes
  
  return (
    <div role="alert" aria-live="polite">
      {timeLeft < 120 && (
        <div className="bg-yellow-100 p-4 rounded">
          <p>
            Votre session expire dans {Math.floor(timeLeft / 60)} minutes.
            <button onClick={extendSession}>Prolonger la session</button>
          </p>
        </div>
      )}
    </div>
  );
}
```

## Plan d'actions OPQUAST

### Critères de qualité web prioritaires

#### 1. Architecture de l'information

**Règle 1 : Le code source de chaque page contient une métadonnée qui en décrit le contenu**
```typescript
// ✅ Déjà implémenté
<Head>
  <title>{t('home.title')}</title>
  <meta name="description" content={t('home.description')} />
</Head>
```

**Règle 5 : Le site propose un plan du site**
```typescript
// ✅ À implémenter
// Créer une page /sitemap avec structure complète
export default function SitemapPage() {
  return (
    <main>
      <h1>Plan du site Penpal AI</h1>
      <nav aria-label="Plan du site">
        <section>
          <h2>Pages principales</h2>
          <ul>
            <li><Link href="/">Accueil</Link></li>
            <li><Link href="/demo">Démonstration</Link></li>
            <li><Link href="/pricing">Tarifs</Link></li>
          </ul>
        </section>
      </nav>
    </main>
  );
}
```

#### 2. Identification et contact

**Règle 15 : Les coordonnées de l'organisme sont facilement accessibles**
```typescript
// ✅ À implémenter
export function ContactInfo() {
  return (
    <address>
      <h3>Nous contacter</h3>
      <p>Email : <a href="mailto:support@penpal-ai.maksou.dev">support@penpal-ai.maksou.dev</a></p>
      <p>Société : Penpal AI</p>
    </address>
  );
}
```

#### 3. Navigation

**Règle 35 : La navigation principale est identique sur toutes les pages**
```typescript
// ✅ Déjà implémenté via Layout.tsx
// ✅ À améliorer avec landmarks
<nav aria-label="Navigation principale" role="navigation">
  {/* Contenu navigation */}
</nav>
```

**Règle 47 : Chaque page affiche une information permettant de connaître son emplacement**
```typescript
// ✅ À implémenter
export function PageHeader({ title, breadcrumb }: PageHeaderProps) {
  return (
    <header>
      <BreadcrumbNavigation items={breadcrumb} />
      <h1>{title}</h1>
    </header>
  );
}
```

#### 4. Formulaires

**Règle 104 : Les messages d'erreur et d'aide sont reliés aux champs de formulaire**
```typescript
// ✅ Partiellement implémenté, à compléter
export function EnhancedFormField({ name, label, help, error }: FormFieldProps) {
  const helpId = `${name}-help`;
  const errorId = `${name}-error`;
  
  return (
    <div>
      <label htmlFor={name}>{label}</label>
      <input
        id={name}
        aria-describedby={`${help ? helpId : ''} ${error ? errorId : ''}`.trim()}
        aria-invalid={error ? 'true' : 'false'}
      />
      {help && <div id={helpId} className="help">{help}</div>}
      {error && <div id={errorId} role="alert" className="error">{error}</div>}
    </div>
  );
}
```

#### 5. Qualité et accessibilité

**Règle 133 : Le contraste entre la couleur du texte et celle de son arrière-plan est élevé**
```css
/* ✅ Configuration à valider et améliorer */
:root {
  --text-primary: #1a1a1a;     /* Ratio 21:1 sur blanc */
  --text-secondary: #4a4a4a;   /* Ratio 9.3:1 sur blanc */
  --text-muted: #6b7280;       /* Ratio 5.9:1 sur blanc - À améliorer */
}

/* Mode sombre */
.dark {
  --text-primary: #ffffff;     /* Ratio 21:1 sur noir */
  --text-secondary: #d1d5db;   /* Ratio 11.8:1 sur noir */
}
```

## Outils et méthodologies

### Tests d'accessibilité

#### 1. Tests automatisés

```json
{
  "scripts": {
    "a11y:test": "axe-core",
    "a11y:ci": "pa11y-ci --sitemap http://localhost:3000/sitemap.xml",
    "lighthouse": "lighthouse --only-categories=accessibility"
  },
  "devDependencies": {
    "@axe-core/playwright": "^4.8.0",
    "pa11y-ci": "^3.0.1",
    "lighthouse": "^10.4.0"
  }
}
```

#### 2. Tests manuels

**Checklist navigation clavier :**
- [ ] Tous les éléments interactifs accessibles via Tab
- [ ] Ordre de tabulation logique
- [ ] Focus visible sur tous les éléments
- [ ] Raccourcis clavier documentés
- [ ] Échappement possible des composants modaux

**Checklist lecteurs d'écran :**
- [ ] Titre de page annoncé
- [ ] Structure de titres cohérente (h1, h2, h3...)
- [ ] Contenu des formulaires compréhensible
- [ ] Navigation et landmarks identifiés
- [ ] Changements dynamiques annoncés

#### 3. Tests avec utilisateurs

```typescript
// ✅ Composant pour collecter feedback accessibilité
export function AccessibilityFeedback() {
  return (
    <form action="/api/feedback/accessibility">
      <fieldset>
        <legend>Retour d'expérience accessibilité</legend>
        
        <div>
          <label htmlFor="assistive-tech">Technologie d'assistance utilisée</label>
          <select id="assistive-tech" name="assistiveTech">
            <option value="">Aucune</option>
            <option value="screen-reader">Lecteur d'écran</option>
            <option value="voice-control">Contrôle vocal</option>
            <option value="keyboard-only">Navigation clavier uniquement</option>
          </select>
        </div>
        
        <div>
          <label htmlFor="difficulty">Difficultés rencontrées</label>
          <textarea id="difficulty" name="difficulty" rows={4}></textarea>
        </div>
        
        <button type="submit">Envoyer le retour</button>
      </fieldset>
    </form>
  );
}
```

### Implémentation progressive

#### Phase 1 : Fondamentaux (Sprint 1-2)
1. **Structure HTML sémantique**
   - Correction des éléments de titre
   - Ajout des landmarks manquants
   - Navigation principale accessible

2. **Formulaires accessibles**
   - Labels associés aux champs
   - Messages d'erreur reliés
   - Instructions d'aide

3. **Navigation clavier**
   - Focus visible sur tous éléments
   - Ordre de tabulation logique
   - Support Escape pour modales

#### Phase 2 : Amélioration (Sprint 3-4)
1. **ARIA avancé**
   - États dynamiques (expanded, selected)
   - Régions live pour chat temps réel
   - Descriptions étendues

2. **Contenu multimédia**
   - Alternatives textuelles complètes
   - Descriptions longues pour graphiques
   - Sous-titres pour vidéos (futures)

3. **Contraste et couleurs**
   - Audit complet des ratios de contraste
   - Correction des combinaisons insuffisantes
   - Tests avec simulateurs de daltonisme

#### Phase 3 : Perfectionnement (Sprint 5-6)
1. **Tests utilisateurs**
   - Sessions avec utilisateurs en situation de handicap
   - Correction des problèmes identifiés
   - Validation des améliorations

2. **Documentation utilisateur**
   - Guide d'accessibilité pour les utilisateurs
   - Raccourcis clavier documentés
   - Aide contextuelle améliorée

3. **Monitoring continu**
   - Tests automatisés en CI/CD
   - Métriques d'accessibilité
   - Processus de revue des nouvelles fonctionnalités

## Mesure et suivi

### KPI d'accessibilité

```typescript
// Métriques à tracker
interface AccessibilityMetrics {
  lighthouse: {
    score: number;        // Score Lighthouse (0-100)
    issues: number;       // Nombre de problèmes détectés
  };
  axe: {
    violations: number;   // Violations axe-core
    incomplete: number;   // Tests incomplets
  };
  manual: {
    keyboardScore: number;    // % éléments accessibles clavier
    screenReaderScore: number; // % contenu lisible lecteur d'écran
  };
  userFeedback: {
    satisfactionScore: number; // Note satisfaction utilisateurs handicapés
    reportedIssues: number;    // Problèmes signalés par utilisateurs
  };
}
```

### Processus de validation

1. **Revue de code** : Checklist accessibilité obligatoire
2. **Tests automatisés** : Échec du CI si violations critiques
3. **Tests manuels** : Validation lecteur d'écran et navigation clavier
4. **Audit périodique** : Évaluation trimestrielle par expert accessibilité

### Formation équipe

#### Développeurs
- Formation RGAA 4.1 et ARIA
- Outils de test (axe DevTools, Lighthouse)
- Bonnes pratiques React/Next.js accessibles

#### Designers
- Contraste et couleurs accessibles
- Design inclusif et utilisabilité
- Conception mobile accessible

#### Product Owners
- Critères d'acceptation incluant accessibilité
- Priorisation des améliorations
- Communication avec utilisateurs handicapés

## Conclusion

L'application Penpal AI présente déjà de bonnes bases d'accessibilité avec une structure sémantique correcte, un support basique ARIA et une navigation fonctionnelle. Le plan d'actions proposé permettra d'atteindre une conformité RGAA niveau AA et d'implémenter les bonnes pratiques OPQUAST essentielles.

La démarche progressive sur 6 sprints garantit une amélioration continue sans impact majeur sur le développement des fonctionnalités, tout en assurant une expérience inclusive pour tous les utilisateurs.

