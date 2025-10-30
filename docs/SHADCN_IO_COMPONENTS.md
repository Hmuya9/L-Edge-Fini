# shadcn.io - AI-Native Component Library

## Overview

[shadcn.io](https://shadcn.io) is a comprehensive, community-driven registry of React components built with TypeScript, Tailwind CSS, and Radix UI. It provides essential UI components, advanced patterns, and AI integrations for modern React applications.

## Key Features

### 1. **Composable React UI Components**
- Fully composable components with TypeScript support
- Built with Tailwind CSS and Radix UI
- Customizable and extensible design system
- MIT licensed and community-driven

### 2. **AI-Powered Components**
- AI chat interfaces and conversational UI
- Intelligent form components
- Smart data visualization
- Natural language processing integrations

### 3. **Advanced Chart Components**
- Built with Recharts for data visualization
- Interactive and responsive charts
- Multiple chart types (Bar, Line, Pie, Area, Radar, Mixed)
- Dashboard-ready components

### 4. **Animation & Effects**
- Captivating TypeScript animations
- Interactive effects and transitions
- Professional depth and engagement
- Tailwind CSS compatible

## Component Categories

### 1. **Essential UI Components**

#### Terminal Component
```typescript
// Interactive terminal with typing animations
import { Terminal } from "@/components/ui/terminal"

export function TerminalDemo() {
  return (
    <Terminal>
      <TerminalLine>$ npm install @repo/terminal</TerminalLine>
      <TerminalLine>✓ Package installed successfully</TerminalLine>
      <TerminalLine>$ pnpm dev</TerminalLine>
      <TerminalLine>▲ Ready in 847ms</TerminalLine>
      <TerminalLine>$</TerminalLine>
    </Terminal>
  )
}
```

#### Credit Card Component
```typescript
// Interactive 3D credit card with flip animation
import { CreditCard } from "@/components/ui/credit-card"

export function CreditCardDemo() {
  return (
    <CreditCard
      cardNumber="**** **** **** 1234"
      cardholder="JOHN DOE"
      expires="12/28"
      cvv="123"
      bank="This card is property of the bank. If found, please return to the nearest branch."
    />
  )
}
```

#### Image Zoom Component
```typescript
// Zoomable image with smooth modal transitions
import { ImageZoom } from "@/components/ui/image-zoom"

export function ImageZoomDemo() {
  return (
    <ImageZoom
      src="/mountain-landscape.jpg"
      alt="Beautiful mountain landscape"
      caption="Click to zoom"
    />
  )
}
```

#### QR Code Component
```typescript
// Generate QR codes with customizable styling
import { QRCode } from "@/components/ui/qr-code"

export function QRCodeDemo() {
  return (
    <QRCode
      value="https://shadcn.io"
      caption="Scan to visit shadcn.io"
      size={200}
      color="#3B82F6"
    />
  )
}
```

#### Color Picker Component
```typescript
// Advanced color selection with multiple formats
import { ColorPicker } from "@/components/ui/color-picker"

export function ColorPickerDemo() {
  const [color, setColor] = useState("#3B82F6")
  
  return (
    <ColorPicker
      value={color}
      onChange={setColor}
      format="hex"
      showAlpha={false}
    />
  )
}
```

### 2. **Chart Components**

#### Bar Chart
```typescript
import { BarChart, Bar, XAxis, YAxis, CartesianGrid, Tooltip, ResponsiveContainer } from "@/components/ui/charts"

export function BarChartDemo() {
  const data = [
    { name: 'Jan', value: 400 },
    { name: 'Feb', value: 300 },
    { name: 'Mar', value: 200 },
    { name: 'Apr', value: 278 },
    { name: 'May', value: 189 },
    { name: 'Jun', value: 239 }
  ]

  return (
    <ResponsiveContainer width="100%" height={300}>
      <BarChart data={data}>
        <CartesianGrid strokeDasharray="3 3" />
        <XAxis dataKey="name" />
        <YAxis />
        <Tooltip />
        <Bar dataKey="value" fill="#3B82F6" />
      </BarChart>
    </ResponsiveContainer>
  )
}
```

#### Line Chart
```typescript
import { LineChart, Line, XAxis, YAxis, CartesianGrid, Tooltip, ResponsiveContainer } from "@/components/ui/charts"

export function LineChartDemo() {
  const data = [
    { name: 'Feb', value: 400 },
    { name: 'Mar', value: 300 },
    { name: 'Apr', value: 200 },
    { name: 'May', value: 278 },
    { name: 'Jun', value: 189 }
  ]

  return (
    <ResponsiveContainer width="100%" height={300}>
      <LineChart data={data}>
        <CartesianGrid strokeDasharray="3 3" />
        <XAxis dataKey="name" />
        <YAxis />
        <Tooltip />
        <Line type="monotone" dataKey="value" stroke="#3B82F6" strokeWidth={2} />
      </LineChart>
    </ResponsiveContainer>
  )
}
```

#### Pie Chart
```typescript
import { PieChart, Pie, Cell, ResponsiveContainer, Tooltip, Legend } from "@/components/ui/charts"

export function PieChartDemo() {
  const data = [
    { name: 'Group A', value: 400 },
    { name: 'Group B', value: 300 },
    { name: 'Group C', value: 300 },
    { name: 'Group D', value: 200 }
  ]

  const COLORS = ['#3B82F6', '#10B981', '#F59E0B', '#EF4444']

  return (
    <ResponsiveContainer width="100%" height={300}>
      <PieChart>
        <Pie
          data={data}
          cx="50%"
          cy="50%"
          labelLine={false}
          label={({ name, percent }) => `${name} ${(percent * 100).toFixed(0)}%`}
          outerRadius={80}
          fill="#8884d8"
          dataKey="value"
        >
          {data.map((entry, index) => (
            <Cell key={`cell-${index}`} fill={COLORS[index % COLORS.length]} />
          ))}
        </Pie>
        <Tooltip />
        <Legend />
      </PieChart>
    </ResponsiveContainer>
  )
}
```

### 3. **Animation Components**

#### Magnetic Effect
```typescript
import { Magnetic } from "@/components/ui/magnetic"

export function MagneticDemo() {
  return (
    <Magnetic>
      <Button>Hover me</Button>
    </Magnetic>
  )
}
```

#### Animated Cursor
```typescript
import { AnimatedCursor } from "@/components/ui/animated-cursor"

export function AnimatedCursorDemo() {
  return (
    <div className="relative">
      <AnimatedCursor />
      <div className="p-8 text-center">
        <p>Move your cursor here</p>
      </div>
    </div>
  )
}
```

#### Liquid Button
```typescript
import { LiquidButton } from "@/components/ui/liquid-button"

export function LiquidButtonDemo() {
  return (
    <LiquidButton>
      Hover me
    </LiquidButton>
  )
}
```

#### Rolling Text
```typescript
import { RollingText } from "@/components/ui/rolling-text"

export function RollingTextDemo() {
  return (
    <RollingText>
      Rolling Animation
    </RollingText>
  )
}
```

#### Shimmering Text
```typescript
import { ShimmeringText } from "@/components/ui/shimmering-text"

export function ShimmeringTextDemo() {
  return (
    <ShimmeringText>
      Shimmering Effect
    </ShimmeringText>
  )
}
```

### 4. **Custom React Hooks**

#### useBoolean
```typescript
import { useBoolean } from "@/hooks/use-boolean"

export function BooleanDemo() {
  const [isEnabled, { toggle, enable, disable }] = useBoolean(false)

  return (
    <div className="space-y-4">
      <div className="flex items-center space-x-2">
        <div className={`w-3 h-3 rounded-full ${isEnabled ? 'bg-green-500' : 'bg-gray-300'}`} />
        <span>{isEnabled ? 'Enabled' : 'Disabled'}</span>
      </div>
      <div className="flex space-x-2">
        <Button onClick={toggle}>Toggle</Button>
        <Button onClick={enable}>Enable</Button>
        <Button onClick={disable}>Disable</Button>
      </div>
    </div>
  )
}
```

#### useCounter
```typescript
import { useCounter } from "@/hooks/use-counter"

export function CounterDemo() {
  const [count, { increment, decrement, reset }] = useCounter(0)

  return (
    <div className="flex items-center space-x-4">
      <Button onClick={decrement}>-</Button>
      <span className="text-2xl font-bold">{count}</span>
      <Button onClick={increment}>+</Button>
      <Button onClick={reset} variant="outline">Reset</Button>
    </div>
  )
}
```

#### useLocalStorage
```typescript
import { useLocalStorage } from "@/hooks/use-local-storage"

export function LocalStorageDemo() {
  const [name, setName] = useLocalStorage('user-name', '')

  return (
    <div className="space-y-4">
      <Input
        placeholder="Enter your name"
        value={name}
        onChange={(e) => setName(e.target.value)}
      />
      <p>Enter name above</p>
      <p className="text-sm text-muted-foreground">Persisted in localStorage</p>
    </div>
  )
}
```

#### useDebounceValue
```typescript
import { useDebounceValue } from "@/hooks/use-debounce-value"

export function DebounceDemo() {
  const [search, setSearch] = useState('')
  const [debouncedSearch] = useDebounceValue(search, 500)
  const [apiCalls, setApiCalls] = useState(0)

  useEffect(() => {
    if (debouncedSearch) {
      setApiCalls(prev => prev + 1)
      // Simulate API call
    }
  }, [debouncedSearch])

  return (
    <div className="space-y-4">
      <Input
        placeholder="Type to search..."
        value={search}
        onChange={(e) => setSearch(e.target.value)}
      />
      <p>Search: "{debouncedSearch}"</p>
      <p>API calls: {apiCalls}</p>
    </div>
  )
}
```

#### useHover
```typescript
import { useHover } from "@/hooks/use-hover"

export function HoverDemo() {
  const [hoverRef, isHovered] = useHover()

  return (
    <div
      ref={hoverRef}
      className="p-4 border rounded-lg cursor-pointer"
    >
      <span className="text-2xl">{isHovered ? '😊' : '🙂'}</span>
      <p>Hover me</p>
    </div>
  )
}
```

#### useCountdown
```typescript
import { useCountdown } from "@/hooks/use-countdown"

export function CountdownDemo() {
  const [timeLeft, { start, stop, reset }] = useCountdown(10)

  return (
    <div className="space-y-4">
      <div className="text-2xl font-bold">{timeLeft}s</div>
      <div className="flex space-x-2">
        <Button onClick={start}>Start</Button>
        <Button onClick={stop}>Stop</Button>
        <Button onClick={reset} variant="outline">Reset</Button>
      </div>
      <p>Countdown Timer</p>
    </div>
  )
}
```

## Installation Guide

### 1. **Install shadcn CLI**
```bash
npx shadcn@latest init
```

### 2. **Add Components**
```bash
# Add specific components
npx shadcn add navbar
npx shadcn add terminal
npx shadcn add credit-card
npx shadcn add qr-code
npx shadcn add color-picker

# Add chart components
npx shadcn add bar-chart
npx shadcn add line-chart
npx shadcn add pie-chart

# Add animation components
npx shadcn add magnetic
npx shadcn add liquid-button
npx shadcn add rolling-text

# Add custom hooks
npx shadcn add use-boolean
npx shadcn add use-counter
npx shadcn add use-local-storage
```

### 3. **Configure TypeScript**
```typescript
// tsconfig.json
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@/*": ["./src/*"],
      "@/components/*": ["./src/components/*"],
      "@/hooks/*": ["./src/hooks/*"],
      "@/lib/*": ["./src/lib/*"]
    }
  }
}
```

## Le Fade Integration Examples

### 1. **Booking Interface with Terminal**
```typescript
// components/BookingTerminal.tsx
import { Terminal } from "@/components/ui/terminal"

export function BookingTerminal() {
  return (
    <Terminal>
      <TerminalLine>$ lefade book --service standard --barber mike</TerminalLine>
      <TerminalLine>✓ Available slots found</TerminalLine>
      <TerminalLine>$ Select time: 2:00 PM - 2:30 PM</TerminalLine>
      <TerminalLine>✓ Booking confirmed</TerminalLine>
      <TerminalLine>$</TerminalLine>
    </Terminal>
  )
}
```

### 2. **Payment with Credit Card**
```typescript
// components/PaymentCard.tsx
import { CreditCard } from "@/components/ui/credit-card"

export function PaymentCard({ paymentInfo }: { paymentInfo: PaymentInfo }) {
  return (
    <CreditCard
      cardNumber={paymentInfo.cardNumber}
      cardholder={paymentInfo.cardholder}
      expires={paymentInfo.expires}
      cvv={paymentInfo.cvv}
      bank="Le Fade Payment Processing"
    />
  )
}
```

### 3. **Analytics Dashboard with Charts**
```typescript
// components/AnalyticsDashboard.tsx
import { BarChart, LineChart, PieChart } from "@/components/ui/charts"

export function AnalyticsDashboard() {
  return (
    <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
      <div className="col-span-1">
        <h3 className="text-lg font-semibold mb-4">Monthly Bookings</h3>
        <BarChart data={monthlyBookings} />
      </div>
      <div className="col-span-1">
        <h3 className="text-lg font-semibold mb-4">Revenue Trend</h3>
        <LineChart data={revenueData} />
      </div>
      <div className="col-span-1">
        <h3 className="text-lg font-semibold mb-4">Service Mix</h3>
        <PieChart data={serviceMix} />
      </div>
    </div>
  )
}
```

### 4. **Interactive Booking Form**
```typescript
// components/BookingForm.tsx
import { useBoolean, useCounter } from "@/hooks"
import { LiquidButton } from "@/components/ui/liquid-button"

export function BookingForm() {
  const [isAdvanced, { toggle }] = useBoolean(false)
  const [guestCount, { increment, decrement }] = useCounter(1)

  return (
    <form className="space-y-6">
      <div className="flex items-center space-x-2">
        <input
          type="checkbox"
          id="advanced"
          checked={isAdvanced}
          onChange={toggle}
        />
        <label htmlFor="advanced">Advanced options</label>
      </div>

      {isAdvanced && (
        <div className="space-y-4">
          <div className="flex items-center space-x-4">
            <span>Guest count:</span>
            <Button onClick={decrement}>-</Button>
            <span className="font-bold">{guestCount}</span>
            <Button onClick={increment}>+</Button>
          </div>
        </div>
      )}

      <LiquidButton type="submit">
        Book Appointment
      </LiquidButton>
    </form>
  )
}
```

## Benefits for Le Fade

### 1. **Professional UI Components**
- Modern, accessible components
- Consistent design system
- TypeScript support for type safety
- Easy customization and theming

### 2. **Enhanced User Experience**
- Interactive animations and effects
- Smooth transitions and micro-interactions
- Responsive design for all devices
- Professional appearance

### 3. **Developer Experience**
- Copy-paste components (no dependencies)
- Full control over code
- Easy to customize and extend
- Comprehensive documentation

### 4. **Performance**
- Optimized for Next.js
- Tree-shakable components
- Minimal bundle size impact
- Fast loading and rendering

## Next Steps

1. **Install shadcn CLI** and initialize the project
2. **Add essential components** for Le Fade's needs
3. **Customize components** to match Le Fade branding
4. **Implement interactive features** for booking and payments
5. **Add analytics components** for the admin dashboard

This component library provides Le Fade with professional, modern UI components that enhance the user experience while maintaining full control over the codebase.


