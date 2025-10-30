# Cal.com - Open Source Scheduling Platform

## Overview

Cal.com is a full open-source scheduling platform that provides comprehensive booking solutions for businesses and individuals. It offers both hosted and self-hosted options, making it perfect for Le Fade's booking needs with the flexibility to embed quickly and migrate later.

## Key Features

### 1. **Core Scheduling Features**
- **Event Types**: Multiple service types (Standard, Deluxe haircuts)
- **Availability Management**: Custom working hours and time zones
- **Booking Rules**: Buffer times, minimum notice, maximum advance booking
- **Recurring Events**: Regular appointment scheduling
- **Time Zone Support**: Automatic time zone detection and conversion

### 2. **Integration Capabilities**
- **Embed Widget**: Easy integration into existing websites
- **API Access**: Full REST API for custom integrations
- **Webhooks**: Real-time event notifications
- **Calendar Sync**: Google Calendar, Outlook, Apple Calendar
- **Payment Integration**: Stripe, PayPal, and other payment providers

### 3. **User Management**
- **Multi-user Support**: Multiple barbers with individual calendars
- **Role-based Access**: Admin, barber, and client roles
- **Team Management**: Organize barbers into teams
- **User Profiles**: Custom profiles with availability and services

### 4. **Customization Options**
- **Branding**: Custom logos, colors, and themes
- **Custom Fields**: Collect specific information during booking
- **Email Templates**: Customizable confirmation and reminder emails
- **SMS Notifications**: Text message reminders and updates

## Implementation for Le Fade

### 1. **Quick Embed Integration**

#### Basic Embed Widget
```html
<!-- Add to Le Fade booking page -->
<div id="cal-embed" style="width:100%;height:100%;overflow:scroll;"></div>
<script type="text/javascript">
  (function (C, A, L) { 
    let p = function (a, ar) { 
      a.q.push(ar); 
    }; 
    let d = C.document; 
    C.Cal = C.Cal || function () { 
      let cal = C.Cal; 
      let ar = arguments; 
      if (!cal.loaded) { 
        cal.ns = {}; 
        cal.q = cal.q || []; 
        d.head.appendChild(d.createElement("script")).src = A; 
        cal.loaded = true; 
      } 
      if (ar[0] === L) { 
        const api = function () { 
          p(api, arguments); 
        }; 
        const namespace = ar[1]; 
        api.q = api.q || []; 
        typeof namespace === "string" ? (cal.ns[namespace] = api) && p(api, ar) : p(cal, ar); 
        return; 
      } 
      p(cal, ar); 
    }; 
  })(window, "https://app.cal.com/embed/embed.js", "init");
  
  Cal("init", "lefade/standard-haircut");
  Cal("inline", {
    elementOrSelector: "#cal-embed",
    layout: "month_view"
  });
</script>
```

#### Advanced Embed with Customization
```typescript
// components/CalEmbed.tsx
interface CalEmbedProps {
  eventType: 'standard' | 'deluxe'
  barberId?: string
  className?: string
}

export function CalEmbed({ eventType, barberId, className }: CalEmbedProps) {
  useEffect(() => {
    // Load Cal.com embed script
    const script = document.createElement('script')
    script.src = 'https://app.cal.com/embed/embed.js'
    script.async = true
    document.head.appendChild(script)

    // Initialize Cal.com
    script.onload = () => {
      if (window.Cal) {
        window.Cal('init', `lefade/${eventType}-haircut`)
        window.Cal('inline', {
          elementOrSelector: '#cal-embed',
          layout: 'month_view',
          theme: 'light',
          branding: {
            brandColor: '#f59e0b', // Amber-500
            lightColor: '#ffffff',
            lightestColor: '#f9fafb',
            hideEventTypeDetails: false,
            hideBranding: false
          },
          prefill: {
            name: '',
            email: '',
            notes: ''
          },
          customData: {
            barberId: barberId || '',
            serviceType: eventType
          }
        })
      }
    }

    return () => {
      document.head.removeChild(script)
    }
  }, [eventType, barberId])

  return (
    <div className={className}>
      <div id="cal-embed" className="w-full h-[600px] rounded-lg border" />
    </div>
  )
}
```

### 2. **API Integration**

#### Create Booking via API
```typescript
// lib/cal-com.ts
interface CalComBooking {
  eventTypeId: number
  start: string
  end: string
  timeZone: string
  responses: {
    name: string
    email: string
    notes?: string
    barberId?: string
    serviceType?: string
  }
}

export async function createCalComBooking(booking: CalComBooking) {
  const response = await fetch('https://api.cal.com/v1/bookings', {
    method: 'POST',
    headers: {
      'Authorization': `Bearer ${process.env.CAL_COM_API_KEY}`,
      'Content-Type': 'application/json'
    },
    body: JSON.stringify(booking)
  })

  if (!response.ok) {
    throw new Error('Failed to create booking')
  }

  return response.json()
}

// Usage in booking page
export async function handleBookingSubmit(formData: FormData) {
  const booking: CalComBooking = {
    eventTypeId: formData.get('serviceType') === 'deluxe' ? 2 : 1,
    start: formData.get('startTime') as string,
    end: formData.get('endTime') as string,
    timeZone: Intl.DateTimeFormat().resolvedOptions().timeZone,
    responses: {
      name: formData.get('name') as string,
      email: formData.get('email') as string,
      notes: formData.get('notes') as string,
      barberId: formData.get('barberId') as string,
      serviceType: formData.get('serviceType') as string
    }
  }

  try {
    const result = await createCalComBooking(booking)
    
    // Sync with Le Fade database
    await syncBookingToDatabase(result)
    
    return { success: true, booking: result }
  } catch (error) {
    return { success: false, error: error.message }
  }
}
```

#### Webhook Integration
```typescript
// app/api/webhooks/cal-com/route.ts
export async function POST(request: Request) {
  const payload = await request.json()
  
  switch (payload.triggerEvent) {
    case 'BOOKING_CREATED':
      await handleBookingCreated(payload.payload)
      break
    case 'BOOKING_CANCELLED':
      await handleBookingCancelled(payload.payload)
      break
    case 'BOOKING_RESCHEDULED':
      await handleBookingRescheduled(payload.payload)
      break
  }
  
  return new Response('OK', { status: 200 })
}

async function handleBookingCreated(booking: any) {
  // Create appointment in Le Fade database
  await db.appointment.create({
    data: {
      calComId: booking.id,
      clientId: booking.responses.email,
      barberId: booking.responses.barberId,
      startAt: new Date(booking.startTime),
      endAt: new Date(booking.endTime),
      status: 'CONFIRMED',
      type: booking.responses.serviceType,
      notes: booking.responses.notes
    }
  })
  
  // Send confirmation email
  await sendBookingConfirmation(booking)
}
```

### 3. **Database Synchronization**

#### Sync Cal.com Events with Le Fade Database
```typescript
// lib/cal-sync.ts
export async function syncCalComBookings() {
  const response = await fetch('https://api.cal.com/v1/bookings', {
    headers: {
      'Authorization': `Bearer ${process.env.CAL_COM_API_KEY}`
    }
  })
  
  const bookings = await response.json()
  
  for (const booking of bookings) {
    await db.appointment.upsert({
      where: { calComId: booking.id },
      update: {
        startAt: new Date(booking.startTime),
        endAt: new Date(booking.endTime),
        status: booking.status === 'confirmed' ? 'CONFIRMED' : 'PENDING',
        notes: booking.responses.notes
      },
      create: {
        calComId: booking.id,
        clientId: booking.responses.email,
        barberId: booking.responses.barberId,
        startAt: new Date(booking.startTime),
        endAt: new Date(booking.endTime),
        status: booking.status === 'confirmed' ? 'CONFIRMED' : 'PENDING',
        type: booking.responses.serviceType,
        notes: booking.responses.notes
      }
    })
  }
}
```

### 4. **Custom Event Types Setup**

#### Standard Haircut Event Type
```json
{
  "title": "Standard Haircut",
  "slug": "standard-haircut",
  "description": "Professional haircut at our shop location",
  "length": 30,
  "price": 3999,
  "currency": "USD",
  "requiresConfirmation": true,
  "disableGuests": false,
  "minimumBookingNotice": 2,
  "beforeEventBuffer": 0,
  "afterEventBuffer": 15,
  "locations": [
    {
      "type": "inPerson",
      "address": "123 Main St, City, State 12345"
    }
  ],
  "customInputs": [
    {
      "label": "Preferred Barber",
      "type": "select",
      "required": false,
      "options": [
        "Any Available",
        "Mike Johnson",
        "Sarah Davis",
        "Alex Chen"
      ]
    },
    {
      "label": "Hair Length",
      "type": "select",
      "required": true,
      "options": [
        "Short (1-2 inches)",
        "Medium (3-4 inches)",
        "Long (5+ inches)"
      ]
    }
  ]
}
```

#### Deluxe Haircut Event Type
```json
{
  "title": "Deluxe Haircut",
  "slug": "deluxe-haircut",
  "description": "Premium haircut service at your location",
  "length": 45,
  "price": 6000,
  "currency": "USD",
  "requiresConfirmation": true,
  "disableGuests": false,
  "minimumBookingNotice": 4,
  "beforeEventBuffer": 0,
  "afterEventBuffer": 15,
  "locations": [
    {
      "type": "inPerson",
      "address": "Customer's Location"
    }
  ],
  "customInputs": [
    {
      "label": "Service Address",
      "type": "text",
      "required": true,
      "placeholder": "Enter your full address"
    },
    {
      "label": "Access Instructions",
      "type": "textarea",
      "required": false,
      "placeholder": "Any special instructions for finding your location"
    },
    {
      "label": "Preferred Barber",
      "type": "select",
      "required": true,
      "options": [
        "Mike Johnson",
        "Sarah Davis",
        "Alex Chen"
      ]
    }
  ]
}
```

## Migration Strategy

### Phase 1: Quick Embed (Week 1)
1. **Set up Cal.com account**
   - Create event types for Standard and Deluxe
   - Configure availability and booking rules
   - Set up payment integration with Stripe

2. **Embed in Le Fade**
   - Add Cal.com embed widget to booking page
   - Customize branding to match Le Fade theme
   - Test booking flow end-to-end

### Phase 2: API Integration (Week 2-3)
1. **API Setup**
   - Generate Cal.com API key
   - Set up webhook endpoints
   - Implement database synchronization

2. **Enhanced Features**
   - Custom booking forms
   - Automated confirmations
   - Calendar sync for barbers

### Phase 3: Full Migration (Month 2)
1. **Self-hosted Option**
   - Deploy Cal.com on own infrastructure
   - Customize for Le Fade specific needs
   - Full control over data and branding

2. **Advanced Features**
   - Custom reporting and analytics
   - Advanced scheduling rules
   - Integration with Le Fade admin dashboard

## Benefits for Le Fade

### 1. **Quick Implementation**
- Embed widget can be live in hours
- No complex development required
- Proven, reliable scheduling system

### 2. **Professional Features**
- Time zone handling
- Automated reminders
- Payment processing
- Calendar synchronization

### 3. **Scalability**
- Handles multiple barbers
- Supports complex scheduling rules
- Grows with business needs

### 4. **Cost-Effective**
- Free tier available
- Pay-as-you-grow pricing
- No infrastructure maintenance

## Next Steps

1. **Sign up for Cal.com** and create event types
2. **Embed widget** in Le Fade booking page
3. **Set up API integration** for database sync
4. **Configure webhooks** for real-time updates
5. **Test booking flow** with real customers

This approach allows Le Fade to get professional scheduling up and running quickly while maintaining the flexibility to migrate to a fully custom solution later.


