# Shadcn/ui Documentation - Design System & App Shell

## Overview

Shadcn/ui is a React components generator that helps build bespoke UI component libraries styled with Tailwind variants. It accommodates a headless philosophy of separating the UI design of a component from its logical internal implementation.

## Key Concepts

### Headless Philosophy
- **UI Design**: Separated from logical implementation
- **Composability**: Built from basic JSX and Radix UI on top of React
- **Tailwind Variants**: Managed by Class Variance Authority (CVA)
- **Customization**: Fully customizable boilerplate code

### Core Packages
```bash
npm install tailwindcss-animate class-variance-authority clsx tailwind-merge lucide-react
```

## Initialization Process

### 1. Initialize Shadcn/ui
```bash
npx shadcn@latest init
```

### 2. Configuration Options
- **Style**: New York (recommended)
- **Base Color**: Zinc (recommended)
- **CSS Variables**: Yes (recommended)

### 3. Generated Configuration
```json
{
  "$schema": "https://ui.shadcn.com/schema.json",
  "style": "New York",
  "rsc": true,
  "tsx": true,
  "tailwind": {
    "config": "tailwind.config.ts",
    "css": "src/app/globals.css",
    "baseColor": "Zinc",
    "cssVariables": true,
    "prefix": ""
  },
  "aliases": {
    "components": "@nextjs-shadcn/components",
    "utils": "@nextjs-shadcn/lib/utils",
    "ui": "@nextjs-shadcn/components/ui",
    "lib": "@nextjs-shadcn/lib",
    "hooks": "@nextjs-shadcn/hooks"
  },
  "iconLibrary": "lucide"
}
```

## Component Generation

### Basic Components
```bash
# Button component
npx shadcn@latest add button

# Input and Label components
npx shadcn@latest add input label

# Pagination components
npx shadcn@latest add pagination

# Form components
npx shadcn@latest add form

# Table components
npx shadcn@latest add table
```

### Generated Component Structure
```typescript
// Button component example
export { Button, buttonVariants }

// Form components example
export {
  useFormField(),
  Form,
  FormItem,
  FormLabel,
  FormControl,
  FormDescription,
  FormMessage,
  FormField
}

// Table components example
export {
  Table,
  TableHeader,
  TableBody,
  TableFooter,
  TableHead,
  TableRow,
  TableCell,
  TableCaption
}
```

## Integration Examples

### React Hook Form Integration
```typescript
"use client"

import { useForm } from "react-hook-form"
import { zodResolver } from "@hookform/resolvers/zod"
import { z } from "zod"
import {
  Form,
  FormControl,
  FormField,
  FormItem,
  FormLabel,
  FormMessage,
} from "@/components/ui/form"
import { Button } from "@/components/ui/button"
import { Input } from "@/components/ui/input"

const formSchema = z.object({
  username: z.string().min(3, {
    message: "Username must be at least 3 characters",
  }),
  email: z.string().email({
    message: "You must enter a valid email",
  })
})

export default function Home() {
  const form = useForm<z.infer<typeof formSchema>>({
    resolver: zodResolver(formSchema),
    defaultValues: {
      username: "",
      email: "",
    },
  })

  function onSubmit(values: z.infer<typeof formSchema>) {
    console.log(values)
  }

  return (
    <div className="w-2/3 mx-auto my-6 border rounded bg-gray-50">
      <h2 className="my-2 p-2 text-3xl text-center font-bold">Create New User</h2>
      <div className="my-2 px-8 py-2">
        <Form {...form}>
          <form onSubmit={form.handleSubmit(onSubmit)} className="space-y-8">
            <FormField
              control={form.control}
              name="username"
              render={({ field }) => (
                <FormItem>
                  <FormLabel>Username</FormLabel>
                  <FormControl>
                    <Input
                      {...field}
                      placeholder="Enter username here"
                    />
                  </FormControl>
                  <FormMessage />
                </FormItem>
              )}
            />
            <FormField
              control={form.control}
              name="email"
              render={({ field }) => (
                <FormItem>
                  <FormLabel>Email</FormLabel>
                  <FormControl>
                    <Input
                      {...field}
                      placeholder="Enter your email"
                    />
                  </FormControl>
                  <FormMessage />
                </FormItem>
              )}
            />
            <Button type="submit">Create User</Button>
          </form>
        </Form>
      </div>
    </div>
  )
}
```

### Tanstack React Table Integration
```typescript
"use client"

import * as React from "react"
import { ColumnDef, SortingState, flexRender, getCoreRowModel, getPaginationRowModel, getSortedRowModel, useReactTable } from "@tanstack/react-table"
import { Table, TableBody, TableCell, TableHead, TableHeader, TableRow } from "@/components/ui/table"
import { Button } from "@/components/ui/button"
import { ArrowUpDown, ChevronLeft, ChevronRight } from "lucide-react"
import { Pagination, PaginationContent, PaginationNext, PaginationPrevious } from "@/components/ui/pagination"

type User = { 
  id: number
  username: string
  email: string
  firstName: string
  lastName: string
  country: string
}

const data: User[] = [
  {
    id: 1,
    username: "heo_haskell",
    email: "heo@haskell.org",
    firstName: "Heo",
    lastName: "Haskell",
    country: "United States",
  },
  // ... more data
]

export default function UsersListPage() {
  const [sorting, setSorting] = React.useState<SortingState>([])

  const columns: ColumnDef<User>[] = [
    {
      accessorKey: "id",
      header: "ID"
    },
    {
      accessorKey: "username",
      header: "Username"
    },
    {
      accessorKey: "firstName",
      header: ({ column }) => {
        return (
          <div className="flex items-center justify-between">
            First Name
            <Button
              className="p-0"
              variant="ghost"
              onClick={() => column.toggleSorting(column.getIsSorted() === "asc")}
            >
              <ArrowUpDown />
            </Button>
          </div>
        )
      },
    },
    // ... more columns
  ]

  const table = useReactTable({
    data,
    columns,
    onSortingChange: setSorting,
    getCoreRowModel: getCoreRowModel(),
    getPaginationRowModel: getPaginationRowModel(),
    getSortedRowModel: getSortedRowModel(),
    state: {
      sorting,
    },
  })

  return (
    <div className="w-full p-2">
      <div className="py-2 text-2xl font-bold">Users List</div>
      <div className="flex items-center justify-between space-x-2 py-4">
        <div className="flex-1 text-sm text-muted-foreground">
          Page {table.getFilteredSelectedRowModel().rows.length} of{" "}
          {table.getPageCount()}
        </div>
        <div className="space-x-2">
          <Pagination>
            <PaginationContent>
              <PaginationPrevious>
                <Button
                  variant="outline"
                  size="sm"
                  onClick={() => table.previousPage()}
                  disabled={!table.getCanPreviousPage()}
                >
                  <ChevronLeft className="h-4 w-4" />
                  Previous
                </Button>
              </PaginationPrevious>
              <PaginationNext>
                <Button
                  variant="outline"
                  size="sm"
                  onClick={() => table.nextPage()}
                  disabled={!table.getCanNextPage()}
                >
                  Next
                  <ChevronRight className="h-4 w-4" />
                </Button>
              </PaginationNext>
            </PaginationContent>
          </Pagination>
        </div>
      </div>
      <Table>
        <TableHeader className="bg-gray-50">
          {table.getHeaderGroups().map((headerGroup) => (
            <TableRow key={headerGroup.id}>
              {headerGroup.headers.map((header) => {
                return (
                  <TableHead key={header.id}>
                    {header.isPlaceholder
                      ? null
                      : flexRender(
                          header.column.columnDef.header,
                          header.getContext()
                        )}
                  </TableHead>
                )
              })}
            </TableRow>
          ))}
        </TableHeader>
        <TableBody>
          {table.getRowModel().rows?.length ? (
            table.getRowModel().rows.map((row) => (
              <TableRow
                key={row.id}
                data-state={row.getIsSelected() && "selected"}
              >
                {row.getVisibleCells().map((cell) => (
                  <TableCell key={cell.id}>
                    {flexRender(
                      cell.column.columnDef.cell,
                      cell.getContext()
                    )}
                  </TableCell>
                ))}
              </TableRow>
            ))
          ) : (
            <TableRow>
              <TableCell
                colSpan={columns.length}
                className="h-24 text-center"
              >
                No results.
              </TableCell>
            </TableRow>
          )}
        </TableBody>
      </Table>
    </div>
  )
}
```

## Theming & Customization

### CSS Variables Approach
```css
:root {
  --background: 0 0% 100%;
  --foreground: 222.2 47.4% 11.2%;
  --muted: 210 40% 96.1%;
  --muted-foreground: 215.4 16.3% 46.9%;
  --popover: 0 0% 100%;
  --popover-foreground: 222.2 47.4% 11.2%;
  --border: 214.3 31.8% 91.4%;
  --input: 214.3 31.8% 91.4%;
  --card: 0 0% 100%;
  --card-foreground: 222.2 47.4% 11.2%;
  --primary: 222.2 47.4% 11.2%;
  --primary-foreground: 210 40% 98%;
  --secondary: 210 40% 96.1%;
  --secondary-foreground: 222.2 47.4% 11.2%;
  --accent: 210 40% 96.1%;
  --accent-foreground: 222.2 47.4% 11.2%;
  --destructive: 0 100% 50%;
  --destructive-foreground: 210 40% 98%;
  --ring: 215 20.2% 65.1%;
  --radius: 0.5rem;
}

.dark {
  --background: 224 71% 4%;
  --foreground: 213 31% 91%;
  --muted: 223 47% 11%;
  --muted-foreground: 215.4 16.3% 56.9%;
  --accent: 216 34% 17%;
  --accent-foreground: 210 40% 98%;
  --popover: 224 71% 4%;
  --popover-foreground: 215 20.2% 65.1%;
  --border: 216 34% 17%;
  --input: 216 34% 17%;
  --card: 224 71% 4%;
  --card-foreground: 213 31% 91%;
  --primary: 210 40% 98%;
  --primary-foreground: 222.2 47.4% 1.2%;
  --secondary: 222.2 47.4% 11.2%;
  --secondary-foreground: 210 40% 98%;
  --destructive: 0 63% 31%;
  --destructive-foreground: 210 40% 98%;
  --ring: 216 34% 17%;
}
```

### Adding New Variants
```javascript
// tailwind.config.js
module.exports = {
  theme: {
    extend: {
      colors: {
        success: "hsl(var(--success))",
        "success-foreground": "hsl(var(--success-foreground))",
      },
    },
  },
}
```

```css
/* globals.css */
:root {
  --success: 38 92% 50%;
  --success-foreground: 48 96% 89%;
}

.dark {
  --success: 48 96% 89%;
  --success-foreground: 38 92% 50%;
}
```

## Component Customization

### Customizing Pagination Components
```typescript
const PaginationPrevious = ({
  className,
  children,
  ...props
}: React.ComponentProps<typeof PaginationLink>) => (
  <PaginationLink
    aria-label="Go to previous page"
    size="default"
    className={cn("gap-1 pl-2.5", className)}
    {...props}
  >
    {children}
  </PaginationLink>
)
PaginationPrevious.displayName = "PaginationPrevious"
```

## Key Benefits

### 1. **Unpackaged Philosophy**
- No package dependencies
- Copy-paste components into your codebase
- Full ownership and customization
- No version conflicts

### 2. **Headless Design**
- UI separated from logic
- Integrate with any headless library
- React Hook Form, Tanstack Table, etc.
- Maximum flexibility

### 3. **Tailwind-First**
- Built for Tailwind CSS
- Consistent design system
- Easy theming and customization
- Performance optimized

### 4. **TypeScript Support**
- Full TypeScript support
- Type-safe component props
- IntelliSense and autocomplete
- Compile-time error checking

## Best Practices

### 1. **Component Composition**
- Build complex components from simple ones
- Use compound component patterns
- Leverage React.forwardRef for ref forwarding
- Implement proper TypeScript interfaces

### 2. **Theming Strategy**
- Use CSS variables for consistent theming
- Implement dark mode support
- Create custom color palettes
- Maintain design system consistency

### 3. **Performance Optimization**
- Use React.memo for expensive components
- Implement proper key props
- Optimize re-renders with useCallback/useMemo
- Lazy load heavy components

### 4. **Accessibility**
- Follow ARIA guidelines
- Implement keyboard navigation
- Ensure screen reader compatibility
- Test with accessibility tools

## Integration with Le Fade

### Current Implementation
- ✅ Button component with variants
- ✅ Input and Label components
- ✅ Card components for plans
- ✅ Toast notifications
- ✅ Skeleton loading states
- ✅ Error boundary components

### Recommended Additions
- 🔄 Form components with validation
- 🔄 Table components for admin dashboard
- 🔄 Pagination for large data sets
- 🔄 Dialog/Modal components
- 🔄 Dropdown/Select components
- 🔄 Date picker for booking
- 🔄 Chart components for metrics

### Design System Alignment
- **Primary Color**: Zinc-900 (matches current theme)
- **Accent Color**: Amber-500 (matches current theme)
- **Typography**: Inter font family
- **Spacing**: Consistent Tailwind spacing scale
- **Border Radius**: 0.5rem default radius

This documentation provides a comprehensive guide for implementing and customizing Shadcn/ui components within the Le Fade project, ensuring consistency and maintainability across the application.
