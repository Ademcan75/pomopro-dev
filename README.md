<p align="center">
  <img src="image/iconfirst.jpeg" alt="Logo" width="200"/>
</p>

# PomoPro - Universal Pomodoro Timer

A sleek, efficient Pomodoro timer application accessible everywhere through modern browsers (desktop & mobile) and as a native Android app.

## 🚀 Features

- Universal accessibility:
  - Progressive Web App (PWA) for desktop and mobile browsers
  - Native Android app
  - Install to desktop directly from browser
  - Offline support on all platforms
- Cross-platform data synchronization
- Rich desktop features via PWA:
  - Desktop notifications
  - Offline mode
  - Install to desktop
  - Start with OS
  - Custom app icon
- Elegant and minimalist user interface
- Smooth animations and transitions
- Customizable timer settings
- Session statistics and history
- Dark/Light theme support
- Responsive design for all screen sizes

## 💻 Tech Stack

### Core Technologies
- React 18
- TypeScript 5.0+
- Bun.js

### Web/PWA (Primary Platform)
- Vite for building
- PWA essentials:
  - Service Workers
  - Web Push API
  - Cache API
  - IndexedDB
- Web Workers for timer accuracy
- Notification API
- Page Visibility API
- Wake Lock API for preventing sleep

### State Management & Data Flow
- React Query v5
- Zustand for local state
- Zod for validation
- WebSocket for real-time updates (it's for next stage)

### Styling & UI
- TailwindCSS or NextUI
- Framer Motion
- CSS Variables for theming
- Media queries for responsiveness

### Testing & Quality
- Vitest for unit testing
- Playwright for E2E testing
- React Testing Library
- Lighthouse for PWA scoring
- ESLint + Prettier

### Performance Monitoring
- Web Vitals tracking
- Error tracking (Sentry)
- Performance monitoring (optional)

## 📱 Application Architecture

```
client/
  ├── web/             # Web application (PWA)
  │   ├── public/
  │   │   ├── manifest.json
  │   │   ├── service-worker.js
  │   │   └── icons/
  │   └── src/
  │       ├── components/
  │       ├── hooks/
  │       ├── pages/
  │       └── utils/
  ├── mobile/         # React Native app
  │   ├── android/
  │   └── src/
  └── shared/         # Shared code between platforms
      ├── components/
      ├── hooks/
      ├── store/
      ├── types/
      └── utils/

server/
  ├── src/
  │   ├── controllers/
  │   ├── models/
  │   ├── routes/
  │   └── services/
  └── tests/
```
## ⚠️ Critical Considerations

### 📊 Analytics & Statistics
- Daily work tracking:
  - Total work hours
  - Number of completed pomodoros
  - Break time usage
- Weekly/Monthly reports:
  - Work patterns
  - Productivity trends
  - Goal completion rates
- Personal insights:
  - Focus patterns
  - Break efficiency
- Progress visualization:
  - Daily/weekly heatmaps
  - Progress charts
  - Streak tracking
- Export capabilities:
  - CSV/PDF reports
  - Data backup

## 💾 Data Models

### Session Tracking
```typescript
interface PomodoroSession {
  id: string;
  startTime: Date;
  endTime: Date;
  duration: number;
  type: 'focus' | 'break';
  completed: boolean;
  interruptions: number;
  category?: string;
  tags?: string[];
  notes?: string;
}

interface DailyStats {
  date: Date;
  totalWorkMinutes: number;
  completedPomodoros: number;
  totalBreakMinutes: number;
  focusScore: number;
  streak: number;
  categories: {
    [category: string]: number;  // minutes per category
  };
}

interface WeeklyStats {
  weekStart: Date;
  weekEnd: Date;
  totalWorkHours: number;
  averageDailyPomodoros: number;
  mostProductiveDay: string;
  topCategories: Array<{
    category: string;
    minutes: number;
  }>;
  weeklyGoalProgress: number;
}
```

### Analytics Dashboard Component
```typescript
interface AnalyticsDashboard {
  timeRange: 'day' | 'week' | 'month' | 'custom';
  metrics: {
    totalTime: number;
    completedSessions: number;
    averageFocusTime: number;
    breakCompliance: number;
    streak: number;
  };
  charts: {
    dailyProgress: ChartData;
    categoryBreakdown: PieChartData;
    hourlyEfficiency: HeatmapData;
  };
}
```

## 📊 Analytics Features Implementation

### Data Collection
```typescript
// Timer Event Tracking
interface TimerEvent {
  type: 'start' | 'pause' | 'resume' | 'complete' | 'interrupt';
  timestamp: Date;
  sessionId: string;
}

// Session Tracker Service
class SessionTracker {
  private currentSession: PomodoroSession;
  private events: TimerEvent[] = [];

  startSession() {
    this.currentSession = {
      id: generateUUID(),
      startTime: new Date(),
      type: 'focus',
      interruptions: 0,
    };
  }

  trackInterruption() {
    this.currentSession.interruptions++;
    this.events.push({
      type: 'interrupt',
      timestamp: new Date(),
      sessionId: this.currentSession.id,
    });
  }

  completeSession() {
    this.currentSession.endTime = new Date();
    this.currentSession.completed = true;
    this.saveSession();
  }
}
```

### Statistics Calculation
```typescript
class StatsCalculator {
  calculateDailyStats(sessions: PomodoroSession[]): DailyStats {
    return {
      date: new Date(),
      totalWorkMinutes: this.calculateTotalWorkTime(sessions),
      completedPomodoros: sessions.filter(s => s.completed).length,
      focusScore: this.calculateFocusScore(sessions),
      // ... other calculations
    };
  }

  calculateFocusScore(sessions: PomodoroSession[]): number {
    // Complex calculation based on:
    // - Session completion rate
    // - Number of interruptions
    // - Break compliance
    // - Session length consistency
  }
}
```

### Progress Tracking
```typescript
interface ProgressTracker {
  dailyGoal: number;  // in minutes or pomodoros
  weeklyGoal: number;
  streakDays: number;
  
  achievements: Achievement[];
  milestones: Milestone[];
}

interface Achievement {  //it's later
  id: string;
  name: string;
  description: string;
  unlockedAt?: Date;
  progress: number;  // 0-100
}
```

### Visualization Components

```typescript
// Daily Progress Chart
const DailyProgressChart: React.FC<{ sessions: PomodoroSession[] }> = ({ sessions }) => {
  const chartData = useMemo(() => formatSessionsForChart(sessions), [sessions]);
  
  return (
    <div className="h-64">
      <LineChart data={chartData}>
        <XAxis dataKey="hour" />
        <YAxis />
        <Line type="monotone" dataKey="focusMinutes" stroke="#8884d8" />
        <Tooltip />
      </LineChart>
    </div>
  );
};

// Focus Heatmap
const FocusHeatmap: React.FC<{ weeklyData: WeeklyStats[] }> = ({ weeklyData }) => {
  // Renders a GitHub-style heatmap of focus sessions
};
```

### Data Export & Backup
```typescript
const exportService = {
  async exportToCsv(dateRange: DateRange): Promise<Blob> {
    const sessions = await fetchSessionsInRange(dateRange);
    return convertToCSV(sessions);
  },

  async exportToPdf(dateRange: DateRange): Promise<Blob> {
    const stats = await calculateStats(dateRange);
    return generatePdfReport(stats);
  },

  async backupData(): Promise<Blob> {
    const allData = await getAllUserData();
    return JSON.stringify(allData);
  }
};
```

## 📱 Mobile-Specific Analytics Features

### Android Native
```typescript
// React Native specific analytics view
const MobileAnalytics: React.FC = () => {
  const stats = useStats();
  
  return (
    <ScrollView>
      <DailyProgressCard stats={stats.daily} />
      <WeeklyOverview stats={stats.weekly} />
      <FocusPatternView data={stats.patterns} />
      <AchievementsSection achievements={stats.achievements} />
    </ScrollView>
  );
};
```

### PWA Analytics
- Offline data collection
- Sync when online
- Compressed data storage
- Battery-efficient calculations

### Timer Accuracy
- Use Web Workers for timer calculations
- Handle background tab timer drift
- Implement server time synchronization
- Account for system sleep/wake
```typescript
// Example of accurate timer implementation
const timerWorker = new Worker('timer-worker.js');
timerWorker.postMessage({ type: 'START', duration: 25 * 60 * 1000 });
timerWorker.onmessage = (e) => {
  if (e.data.type === 'TICK') {
    updateTimerUI(e.data.timeRemaining);
  }
};
```

### Data Persistence
- IndexedDB for web storage
- Handle storage limits
- Implement data backup
- Sync conflict resolution
```typescript
interface StorageStrategy {
  web: 'indexedDB';
  mobile: 'asyncStorage';
  fallback: 'localStorage';
  sync: 'websocket';
}
```

### Offline Support
- Cache necessary assets
- Queue actions when offline
- Sync when back online
- Handle version updates

### Security Considerations
- Implement CSRF protection
- Use HTTP-only cookies
- Sanitize user inputs
- Implement rate limiting
- Use Content Security Policy
```typescript
// Example CSP configuration
{
  directives: {
    defaultSrc: ["'self'"],
    scriptSrc: ["'self'", "'wasm-unsafe-eval'"],
    styleSrc: ["'self'", "'unsafe-inline'"],
    connectSrc: ["'self'", "wss://your-api.com"],
    frameSrc: ["'none'"],
    objectSrc: ["'none'"]
  }
}
```

### Cross-Platform Compatibility

#### Browser Support
- Chrome 80+
- Firefox 75+
- Safari 13.1+
- Edge 80+
- iOS Safari 13.4+
- Chrome for Android 80+

#### Known Platform Differences
| Feature | PWA | Android Native | Solution |
|---------|-----|----------------|----------|
| Background Timer | Limited | Full Support | Web Workers + Notifications |
| Push Notifications | Requires Permission | Built-in | Unified Notification Service |
| Offline Storage | IndexedDB | AsyncStorage | Abstract Storage Layer |
| Deep Links | URL Schemes | Intent Filters | Universal Links Handler |

## 🛠 Development Setup

[Previous Installation section remains the same]

### Environment Configuration

```bash
# .env.example
VITE_API_URL=http://localhost:3000
VITE_WS_URL=ws://localhost:3000
VITE_PUBLIC_VAPID_KEY=your_vapid_key
VITE_SENTRY_DSN=your_sentry_dsn
```

### Development Tools Setup

1. VS Code Extensions:
```json
{
  "recommendations": [
    "dbaeumer.vscode-eslint",
    "esbenp.prettier-vscode",
    "bradlc.vscode-tailwindcss",
  ]
}
```

2. Git Hooks:
```bash
npx husky install
npx husky add .husky/pre-commit "npm run lint && npm run test"
```

## 🔍 Quality Assurance

### Testing Strategy

1. Unit Tests:
```bash
# Run unit tests
npm run test:unit

# Run with coverage
npm run test:coverage
```

2. E2E Tests:
```bash
# Run E2E tests
npm run test:e2e

# Run specific browser
npm run test:e2e:chrome
```

3. Performance Testing:
```bash
# Run Lighthouse CI
npm run lighthouse

# Run bundle analysis
npm run analyze
```

### Error Handling

```typescript
// Global error boundary
class GlobalErrorBoundary extends React.Component {
  static getDerivedStateFromError(error: Error) {
    Sentry.captureException(error);
    return { hasError: true };
  }
}

// API error handling
const handleApiError = (error: ApiError) => {
  if (error.isNetworkError) {
    addToOfflineQueue(error.request);
  }
  if (error.isAuthError) {
    refreshToken();
  }
};
```

## 📱 Progressive Enhancement

### Feature Detection
```typescript
const features = {
  serviceWorker: 'serviceWorker' in navigator,
  notifications: 'Notification' in window,
  wakeLock: 'wakeLock' in navigator,
  share: 'share' in navigator,
};
```

### Fallback Strategies
1. Timer: RequestAnimationFrame → setTimeout → server-side
2. Storage: IndexedDB → WebSQL → LocalStorage
3. Notifications: Native → Web Push → In-app

## 🔄 State Management

### Data Flow
```typescript
interface TimerState {
  status: 'idle' | 'running' | 'paused' | 'completed';
  timeRemaining: number;
  currentSession: Session;
  history: Session[];
}

interface Session {
  id: string;
  startTime: Date;
  duration: number;
  type: 'focus' | 'break';
  completed: boolean;
}
```

## 📈 Monitoring & Analytics

### Key Metrics
- Timer accuracy
- Offline usage
- Sync success rate
- Session completion rate
- User engagement

### Performance Monitoring
```typescript
import { reportWebVitals } from 'web-vitals';

reportWebVitals(({ name, value }) => {
  if (name === 'FCP') {
    console.log(`First Contentful Paint: ${value}`);
  }
});
```

## 🚀 Deployment Checklist

### Pre-deployment
- [ ] Run all tests
- [ ] Check PWA score
- [ ] Verify offline functionality
- [ ] Test cross-browser compatibility
- [ ] Validate service worker
- [ ] Check performance metrics
- [ ] Verify security headers

### Production Configuration
```nginx
# Nginx configuration for PWA
location / {
  add_header Service-Worker-Allowed /;
  add_header Cache-Control "public, max-age=31536000";
  try_files $uri $uri/ /index.html;
}
```


## 🛠 Setup and Installation

### Prerequisites
- Node.js >= 18.0.0
- Bun >= 1.0.0
- Android Studio (for mobile development)
- Modern browser (Chrome, Firefox, Edge, or Safari)

### Development Setup

1. Clone the repository:
```bash
git clone https://github.com/Ademcan75/pomopro.git
cd pomopro
```

2. Install dependencies:
```bash
# Install server dependencies
cd server
bun install

# Install shared dependencies
cd ../client/shared
npm install

# Install web/PWA dependencies
cd ../web
npm install

# Install mobile client dependencies
cd ../mobile
npm install
```

3. Start development servers:
```bash
# Start backend server
cd server
bun dev

# Start web/PWA client
cd ../client/web
npm run dev

# Start mobile client
cd ../client/mobile
npm run android
```

## 📋 Available Scripts

### Backend
- `bun dev`: Start development server
- `bun test`: Run tests
- `bun build`: Build for production

### Web/PWA
- `npm run dev`: Start development server
- `npm run build`: Build for production
- `npm run preview`: Preview production build
- `npm run generate-sw`: Generate service worker
- `npm run optimize-images`: Optimize images for PWA

### Mobile Client
- `npm run android`: Run on Android device/emulator
- `npm run build:android`: Build Android APK
- `npm test`: Run tests

## 🌐 Deployment

### Web/PWA Deployment
- Build the web app: `npm run build`
- Deploy to your preferred hosting service:
  - Vercel (recommended)
  - Netlify
  - GitHub Pages
- Ensure SSL certificate is configured
- Configure caching headers for optimal PWA performance

### Mobile Deployment
- Generate signed APK
- Deploy to Google Play Store

## 🔧 PWA Features Configuration

### Manifest Settings
```json
{
  "name": "PomoPro Timer",
  "short_name": "PomoPro",
  "start_url": "/",
  "display": "standalone",
  "background_color": "#ffffff",
  "theme_color": "#000000",
  "icons": [
    {
      "src": "/icons/icon-192x192.png",
      "sizes": "192x192",
      "type": "image/png"
    },
    {
      "src": "/icons/icon-512x512.png",
      "sizes": "512x512",
      "type": "image/png"
    }
  ]
}
```

### Service Worker Features
- Offline support
- Background sync
- Push notifications
- Cache management
- Update flow

## 📈 Future Enhancements

- Cloud synchronization across devices
- Team collaboration features (Websocket)
- Custom sound effects
- Advanced statistics and analytics
- Integration with task management tools
- iOS native app
- Browser extension
- Custom themes and templates
- Pomodoro technique variations
- Focus music integration

## 📜 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 👥 Contributing

Contributions are welcome! Please read our [Contributing Guide](CONTRIBUTING.md) for details on our code of conduct and the process for submitting pull requests.
