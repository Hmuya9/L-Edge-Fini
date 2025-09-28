# Vercel Next.js Commerce - Production UI/UX Patterns

## Overview

Vercel Next.js Commerce is a production-ready e-commerce template that demonstrates best practices for building scalable, performant commerce applications with Next.js. It provides a comprehensive foundation for modern e-commerce experiences.

## Key Production Patterns

### 1. **Performance Optimization**

#### Image Optimization
```typescript
import Image from 'next/image'

// Optimized product images with lazy loading
<Image
  src={product.image}
  alt={product.name}
  width={400}
  height={400}
  priority={isAboveFold}
  placeholder="blur"
  blurDataURL="data:image/jpeg;base64,..."
  sizes="(max-width: 768px) 100vw, (max-width: 1200px) 50vw, 33vw"
/>
```

#### Code Splitting & Lazy Loading
```typescript
// Dynamic imports for heavy components
const ProductModal = dynamic(() => import('./ProductModal'), {
  loading: () => <Skeleton className="h-96 w-full" />,
  ssr: false
})

// Route-based code splitting
const CheckoutPage = dynamic(() => import('./CheckoutPage'), {
  loading: () => <CheckoutSkeleton />
})
```

#### Caching Strategies
```typescript
// ISR for product pages
export async function getStaticProps({ params }) {
  const product = await getProduct(params.slug)
  
  return {
    props: { product },
    revalidate: 60, // Revalidate every minute
  }
}

// API route caching
export async function GET(request: Request) {
  const products = await getProducts()
  
  return NextResponse.json(products, {
    headers: {
      'Cache-Control': 'public, s-maxage=60, stale-while-revalidate=300'
    }
  })
}
```

### 2. **Routing & Navigation Patterns**

#### Dynamic Product Routes
```typescript
// app/products/[slug]/page.tsx
export async function generateStaticParams() {
  const products = await getProducts()
  
  return products.map((product) => ({
    slug: product.slug,
  }))
}

export async function generateMetadata({ params }) {
  const product = await getProduct(params.slug)
  
  return {
    title: product.name,
    description: product.description,
    openGraph: {
      title: product.name,
      description: product.description,
      images: [product.image],
    },
  }
}
```

#### Breadcrumb Navigation
```typescript
// components/Breadcrumb.tsx
interface BreadcrumbProps {
  items: Array<{
    label: string
    href?: string
  }>
}

export function Breadcrumb({ items }: BreadcrumbProps) {
  return (
    <nav aria-label="Breadcrumb" className="flex items-center space-x-2">
      {items.map((item, index) => (
        <Fragment key={index}>
          {index > 0 && <ChevronRight className="h-4 w-4 text-muted-foreground" />}
          {item.href ? (
            <Link href={item.href} className="text-sm text-muted-foreground hover:text-foreground">
              {item.label}
            </Link>
          ) : (
            <span className="text-sm font-medium">{item.label}</span>
          )}
        </Fragment>
      ))}
    </nav>
  )
}
```

#### Search & Filtering
```typescript
// app/search/page.tsx
export default function SearchPage({ searchParams }) {
  const { q, category, sort, page } = searchParams
  
  return (
    <div className="container mx-auto px-4 py-8">
      <div className="grid grid-cols-1 lg:grid-cols-4 gap-8">
        {/* Filters Sidebar */}
        <aside className="lg:col-span-1">
          <ProductFilters 
            category={category}
            onFilterChange={handleFilterChange}
          />
        </aside>
        
        {/* Products Grid */}
        <main className="lg:col-span-3">
          <ProductGrid 
            query={q}
            category={category}
            sort={sort}
            page={page}
          />
        </main>
      </div>
    </div>
  )
}
```

### 3. **E-commerce Specific Components**

#### Product Card
```typescript
// components/ProductCard.tsx
interface ProductCardProps {
  product: Product
  variant?: 'default' | 'compact' | 'featured'
}

export function ProductCard({ product, variant = 'default' }: ProductCardProps) {
  const [isHovered, setIsHovered] = useState(false)
  
  return (
    <Card 
      className="group cursor-pointer transition-all duration-200 hover:shadow-lg"
      onMouseEnter={() => setIsHovered(true)}
      onMouseLeave={() => setIsHovered(false)}
    >
      <div className="relative aspect-square overflow-hidden rounded-t-lg">
        <Image
          src={product.image}
          alt={product.name}
          fill
          className="object-cover transition-transform duration-200 group-hover:scale-105"
        />
        
        {/* Quick Actions Overlay */}
        <div className={`absolute inset-0 bg-black/20 opacity-0 transition-opacity duration-200 group-hover:opacity-100 ${isHovered ? 'opacity-100' : ''}`}>
          <div className="absolute bottom-4 left-4 right-4 flex space-x-2">
            <Button size="sm" variant="secondary" className="flex-1">
              Quick View
            </Button>
            <Button size="sm" className="flex-1">
              Add to Cart
            </Button>
          </div>
        </div>
      </div>
      
      <CardContent className="p-4">
        <h3 className="font-semibold text-lg mb-2 line-clamp-2">
          {product.name}
        </h3>
        
        <div className="flex items-center justify-between mb-2">
          <div className="flex items-center space-x-2">
            <span className="text-2xl font-bold">${product.price}</span>
            {product.comparePrice && (
              <span className="text-sm text-muted-foreground line-through">
                ${product.comparePrice}
              </span>
            )}
          </div>
          
          {/* Rating */}
          <div className="flex items-center space-x-1">
            <Star className="h-4 w-4 fill-yellow-400 text-yellow-400" />
            <span className="text-sm text-muted-foreground">
              {product.rating} ({product.reviewCount})
            </span>
          </div>
        </div>
        
        {/* Variants */}
        {product.variants && (
          <div className="flex space-x-2 mb-3">
            {product.variants.map((variant) => (
              <div
                key={variant.id}
                className="w-6 h-6 rounded-full border-2 border-gray-200"
                style={{ backgroundColor: variant.color }}
                title={variant.name}
              />
            ))}
          </div>
        )}
        
        {/* Add to Cart Button */}
        <Button className="w-full" size="sm">
          Add to Cart
        </Button>
      </CardContent>
    </Card>
  )
}
```

#### Shopping Cart
```typescript
// components/Cart.tsx
export function Cart() {
  const { items, updateQuantity, removeItem, total } = useCart()
  
  return (
    <Sheet>
      <SheetTrigger asChild>
        <Button variant="outline" size="sm" className="relative">
          <ShoppingCart className="h-4 w-4" />
          {items.length > 0 && (
            <Badge className="absolute -top-2 -right-2 h-5 w-5 rounded-full p-0 text-xs">
              {items.length}
            </Badge>
          )}
        </Button>
      </SheetTrigger>
      
      <SheetContent className="w-full sm:max-w-lg">
        <SheetHeader>
          <SheetTitle>Shopping Cart</SheetTitle>
        </SheetHeader>
        
        <div className="flex flex-col h-full">
          {/* Cart Items */}
          <div className="flex-1 overflow-y-auto py-4">
            {items.length === 0 ? (
              <div className="text-center py-8">
                <ShoppingCart className="h-12 w-12 mx-auto text-muted-foreground mb-4" />
                <p className="text-muted-foreground">Your cart is empty</p>
              </div>
            ) : (
              <div className="space-y-4">
                {items.map((item) => (
                  <CartItem
                    key={item.id}
                    item={item}
                    onUpdateQuantity={updateQuantity}
                    onRemove={removeItem}
                  />
                ))}
              </div>
            )}
          </div>
          
          {/* Cart Summary */}
          {items.length > 0 && (
            <div className="border-t pt-4">
              <div className="flex justify-between items-center mb-4">
                <span className="text-lg font-semibold">Total</span>
                <span className="text-lg font-bold">${total}</span>
              </div>
              
              <Button className="w-full" size="lg">
                Proceed to Checkout
              </Button>
            </div>
          )}
        </div>
      </SheetContent>
    </Sheet>
  )
}
```

#### Checkout Flow
```typescript
// app/checkout/page.tsx
export default function CheckoutPage() {
  const [step, setStep] = useState(1)
  const [formData, setFormData] = useState({})
  
  const steps = [
    { id: 1, title: 'Shipping', description: 'Delivery information' },
    { id: 2, title: 'Payment', description: 'Payment method' },
    { id: 3, title: 'Review', description: 'Order summary' },
  ]
  
  return (
    <div className="container mx-auto px-4 py-8">
      {/* Progress Steps */}
      <div className="mb-8">
        <div className="flex items-center justify-between">
          {steps.map((stepItem, index) => (
            <div key={stepItem.id} className="flex items-center">
              <div className={`flex items-center justify-center w-8 h-8 rounded-full border-2 ${
                step >= stepItem.id 
                  ? 'bg-primary border-primary text-primary-foreground' 
                  : 'border-muted-foreground text-muted-foreground'
              }`}>
                {step > stepItem.id ? (
                  <Check className="h-4 w-4" />
                ) : (
                  <span className="text-sm font-medium">{stepItem.id}</span>
                )}
              </div>
              
              <div className="ml-3">
                <p className={`text-sm font-medium ${
                  step >= stepItem.id ? 'text-foreground' : 'text-muted-foreground'
                }`}>
                  {stepItem.title}
                </p>
                <p className="text-xs text-muted-foreground">
                  {stepItem.description}
                </p>
              </div>
              
              {index < steps.length - 1 && (
                <div className={`w-16 h-0.5 mx-4 ${
                  step > stepItem.id ? 'bg-primary' : 'bg-muted-foreground'
                }`} />
              )}
            </div>
          ))}
        </div>
      </div>
      
      {/* Step Content */}
      <div className="grid grid-cols-1 lg:grid-cols-3 gap-8">
        <div className="lg:col-span-2">
          {step === 1 && <ShippingForm />}
          {step === 2 && <PaymentForm />}
          {step === 3 && <OrderReview />}
        </div>
        
        <div className="lg:col-span-1">
          <OrderSummary />
        </div>
      </div>
    </div>
  )
}
```

### 4. **Performance Monitoring**

#### Web Vitals Tracking
```typescript
// lib/analytics.ts
export function reportWebVitals(metric: NextWebVitalsMetric) {
  // Send to analytics service
  if (metric.label === 'web-vital') {
    gtag('event', metric.name, {
      value: Math.round(metric.value),
      event_category: 'Web Vitals',
      event_label: metric.id,
      non_interaction: true,
    })
  }
}

// app/layout.tsx
export function reportWebVitals(metric: NextWebVitalsMetric) {
  reportWebVitals(metric)
}
```

#### Error Boundaries
```typescript
// components/ErrorBoundary.tsx
export class ErrorBoundary extends Component<Props, State> {
  constructor(props: Props) {
    super(props)
    this.state = { hasError: false, error: null }
  }
  
  static getDerivedStateFromError(error: Error): State {
    return { hasError: true, error }
  }
  
  componentDidCatch(error: Error, errorInfo: ErrorInfo) {
    // Log to error reporting service
    console.error('Error caught by boundary:', error, errorInfo)
  }
  
  render() {
    if (this.state.hasError) {
      return (
        <div className="min-h-screen flex items-center justify-center">
          <div className="text-center">
            <h2 className="text-2xl font-bold mb-4">Something went wrong</h2>
            <Button onClick={() => window.location.reload()}>
              Try again
            </Button>
          </div>
        </div>
      )
    }
    
    return this.props.children
  }
}
```

### 5. **SEO & Meta Optimization**

#### Dynamic Meta Tags
```typescript
// app/products/[slug]/page.tsx
export async function generateMetadata({ params }): Promise<Metadata> {
  const product = await getProduct(params.slug)
  
  return {
    title: `${product.name} - Le Fade`,
    description: product.description,
    keywords: product.tags.join(', '),
    openGraph: {
      title: product.name,
      description: product.description,
      images: [
        {
          url: product.image,
          width: 1200,
          height: 630,
          alt: product.name,
        },
      ],
      type: 'product',
      siteName: 'Le Fade',
    },
    twitter: {
      card: 'summary_large_image',
      title: product.name,
      description: product.description,
      images: [product.image],
    },
    alternates: {
      canonical: `/products/${product.slug}`,
    },
  }
}
```

#### Structured Data
```typescript
// components/ProductStructuredData.tsx
export function ProductStructuredData({ product }: { product: Product }) {
  const structuredData = {
    "@context": "https://schema.org",
    "@type": "Product",
    "name": product.name,
    "description": product.description,
    "image": product.image,
    "brand": {
      "@type": "Brand",
      "name": "Le Fade"
    },
    "offers": {
      "@type": "Offer",
      "price": product.price,
      "priceCurrency": "USD",
      "availability": "https://schema.org/InStock",
      "seller": {
        "@type": "Organization",
        "name": "Le Fade"
      }
    },
    "aggregateRating": {
      "@type": "AggregateRating",
      "ratingValue": product.rating,
      "reviewCount": product.reviewCount
    }
  }
  
  return (
    <script
      type="application/ld+json"
      dangerouslySetInnerHTML={{ __html: JSON.stringify(structuredData) }}
    />
  )
}
```

## Key Takeaways for Le Fade

### 1. **Performance First**
- Implement ISR for product pages
- Use Next.js Image optimization
- Implement proper caching strategies
- Monitor Web Vitals

### 2. **User Experience**
- Smooth checkout flow with progress indicators
- Quick actions on product cards
- Responsive design for all devices
- Error boundaries and fallbacks

### 3. **SEO & Discoverability**
- Dynamic meta tags for each product
- Structured data for rich snippets
- Optimized URLs and breadcrumbs
- Sitemap generation

### 4. **Scalability**
- Component-based architecture
- Type-safe API routes
- Proper error handling
- Performance monitoring

These patterns provide a solid foundation for building a production-ready e-commerce experience that can scale with your business needs.
