## Next.js App Router Course - Starter

This is the starter template for the Next.js App Router Course. It contains the starting code for the dashboard application.

For more information, see the [course curriculum](https://nextjs.org/learn) on the Next.js Website.

1. For run in dev mode:

   ```bash
   $ pnpm run dev
   ```

1. Resolve error in \*.css files (`@tailwind base;`):

   1. Install `Tailwind CSS IntelliSense` add-on for VS Code

   1. Create or edit `.vscode/settings.json` and add:

      ```json
      {
        "files.associations": {
          "*.css": "tailwindcss"
        }
      }
      ```

1. Import `/app/ui/global.css` into `/app/layout.css`:

   ```css
   import "@/app/ui/global.css"
   ...
   ```

1. Adding primary font to app:

   1. Create `/app/ui/fonts.tx` file:

      ```ts
      import { Inter } from "next/font/google"

      export const inter = Inter({ subsets: ["latin"] })
      ```

   1. Import font to `/app/ui/layout.tsx`:

      ```tsx
      import { inter } from "@/app/ui/fonts"
      ```

   1. Use this font:

      ```tsx
      <body className={`${inter.className} antialiased`}>{children}</body>
      ```

1. Adding primary font (`Lusitana`) to app:

   1. Add font to `/app/ui/fonts.ts`:

      ```tsx
      import { Inter, Lusitana } from "next/font/google"

      export const inter = Inter({ subsets: ["latin"] })

      export const lusitana = Lusitana({
        weight: ["400", "700"],
        subsets: ["latin"],
      })
      ```

   1. Import font to `/app/ui/page.tsx`:

      ```tsx
      import { lusitana } from "@/app/ui/fonts"
      ```

   1. Use this font:

      ```tsx
      <p className={`${lusitana.className} text-xl text-gray-800 md:text-3xl md:leading-normal`}>
      ```

1. Adding a images

   1. Import `Image` component into `/app/ui/page.tsx`:

      ```tsx
      import Image from "next/image"
      ```

   1. Use `Image` component:

      ```tsx
      <Image
        src="/hero-desktop.png"
        width={1000}
        height={760}
        className="hidden md:block"
        alt="Screenshots of the dashboard project showing desktop version"
      />
      ```

1. Layouts and Pages

   Next.js uses file-system routing where folders are used to create nested routes. Each folder represents a route segment that maps to a URL segment:

   ![Folders2URLs](/public/nextjs/folders-to-url-segments.avif)

   `page.tsx` is a special `Next.js` file that exports a React component, and it's required for the route to be accessible. In your application, you already have a page file: `/app/page.tsx` - this is the home page associated with the route `/`.

   1. Create folder & file `/app/dashboard/page.tsx`:

      ```tsx
      export default function Page() {
        return <p>Dashboard Page</p>
      }
      ```

   1. Go to [http://localhost:3000/dashboard](http://localhost:3000/dashboard) for look this page

   1. Creating similarly routes [http://localhost:3000/dashboard/customers](http://localhost:3000/dashboard/customers) & [http://localhost:3000/dashboard/invoices](http://localhost:3000/dashboard/invoices)

   1. Common elements shared between some pages (like navigation) carried out using `layout.tsx` file

      1. Create file `/app/dashboard/layout.tsx`:

         ```tsx
         import SideNav from "@/app/ui/dashboard/sidenav"

         export default function Layout({
           children,
         }: {
           children: React.ReactNode
         }) {
           return (
             <div className="flex h-screen flex-col md:flex-row md:overflow-hidden">
               <div className="w-full flex-none md:w-64">
                 <SideNav />
               </div>
               <div className="flex-grow p-6 md:overflow-y-auto md:p-12">
                 {children}
               </div>
             </div>
           )
         }
         ```

1. Navigating Between Pages

   Use `<Link>` component to do `client-side navigation` with JavaScript insteed `<a>` HTML hyperlink tag:

   1. Replace the `<a>` tag with `<Link>` into `/app/ui/dashboard/nav-links.tsx` file:

      ```tsx
      import Link from "next/link"
      ...
      <Link
         key={link.name}
         //...
         <p className="hidden md:block">{link.name}</p>
      </Link>
      ```

   1. Next.js provides a hook `usePathname()` to check the current path.

      For use React hooks need turn `nav-links.tsx` into a **Client Component** by declaretion `"use client"`.

      Declare **Client Component** & inport hook `usePathname()` into `/app/ui/dashboard/nav-links.tsx`:

      ```tsx
      "use client"
      //...
      import { usePathname } from "next/navigation"
      //...
      export default function NavLinks() {
        const pathname = usePathname()
        // ...
      }
      ```

   1. Use the `clsx` library to conditionally apply class names when the link is active. When `link.href` matches the `pathname`, the link should be displayed with blue text and a light blue background:

      ```tsx
      import Link from "next/link"
      import { usePathname } from "next/navigation"
      import clsx from "clsx"
      //...
      export default function NavLinks() {
      const pathname = usePathname()

         return (
            <>
               //..
               <Link
                  key={link.name}
                  href={link.href}
                  className={clsx(
                     "flex h-[48px] grow items-center justify-center gap-2 rounded-md bg-gray-50 p-3 text-sm font-medium hover:bg-sky-100 hover:text-blue-600 md:flex-none md:justify-start md:p-2 md:px-3",
                     {
                        "bg-sky-100 text-blue-600": pathname === link.href,
                     }
                  )}
               >
               //...
            </>
         )
      }
      ```

1. Setting Up the Database

   For working on dashboard need some data. Setting up a `PostgreSQL` database using `@vercel/postgres`.

   1. Create GitHub repository & push project.

   1. Create [vercel](https://vercel.com) project from GitHub repository.

   1. Add `Postgres` storage, copy data from `.env.local`, rename `.env.example` file into local project to `.env` & put into it data from vercel `.env.local` (make sure `.env` is in the ignored into `.gitignore` file).

   1. Install `@vercel/postgres` package:

      ```bash
      pnpm i @vercel/postgres
      ```

   1. Uncomment `/app/seed/rout.ts`file.

      This file is a `Next.js Route Handler` that will be used to seed the database.
      This creates a server-side endpoint that can access in the browser to start populating the database.

   1. Navigate to [localhost:3000/seed](localhost:3000/seed) in the browser.

   1. When finished, will be a message "Database seeded successfully" in the browser. Once completed, can delete this file.

1. Fetching Data

   Next.js can create API endpoints using **Route Handlers**.

   For relational databases like `Postgres` logic to interact do with SQL or with an ORM.

   There are a few cases where have to write database queries:

   - When creating API endpoints, need to write logic to interact with the database.
   - If using **React Server Components** (fetching data on the server), can skip the API layer, and query the database directly without risking exposing the database secrets to the client.

   By default, Next.js applications use **React Server Components**. Fetching data with **Server Components** is a relatively new approach and there are a few benefits of using them:

   - **Server Components** support promises, providing a simpler solution for asynchronous tasks like data fetching. You can use `async/await` syntax without reaching out for `useEffect`, `useState` or data fetching libraries.
   - **Server Components** execute on the server, so you can keep expensive data fetches and logic on the server and only send the result to the client.
   - As mentioned before, since **Server Components** execute on the server, you can query the database directly without an additional API layer.

   For this dashboard project, will write database queries using the **Vercel Postgres SDK** and **SQL**. There are a few reasons why we'll be using SQL:

   - SQL is the industry standard for querying relational databases (e.g. ORMs generate SQL under the hood).
   - Having a basic understanding of SQL can help you understand the fundamentals of relational databases, allowing you to apply your knowledge to other tools.
   - SQL is versatile, allowing you to fetch and manipulate specific data.
   - The Vercel Postgres SDK provides protection against SQL injections.
   - Don't worry if you haven't used SQL before - we have provided the queries for you.

   Into `/app/lib/data.ts` file importing the `sql` function from `@vercel/postgres`. This function allows to query the database:

   ```tsx
   import { sql } from "@vercel/postgres"
   // ...
   ```

   You can call `sql` inside any **Server Component**.

   1. Fetching data for the dashboard overview page

      Fill `/app/dashboard/page.tsx` file:

      ```tsx
      import { Card } from "@/app/ui/dashboard/cards"
      import RevenueChart from "@/app/ui/dashboard/revenue-chart"
      import LatestInvoices from "@/app/ui/dashboard/latest-invoices"
      import { lusitana } from "@/app/ui/fonts"

      export default async function Page() {
        return (
          <main>
            <h1 className={`${lusitana.className} mb-4 text-xl md:text-2xl`}>
              Dashboard
            </h1>
            <div className="grid gap-6 sm:grid-cols-2 lg:grid-cols-4">
              {/* <Card title="Collected" value={totalPaidInvoices} type="collected" /> */}
              {/* <Card title="Pending" value={totalPendingInvoices} type="pending" /> */}
              {/* <Card title="Total Invoices" value={numberOfInvoices} type="invoices" /> */}
              {/* <Card
                     title="Total Customers"
                     value={numberOfCustomers}
                     type="customers"
                  /> */}
            </div>
            <div className="mt-6 grid grid-cols-1 gap-6 md:grid-cols-4 lg:grid-cols-8">
              {/* <RevenueChart revenue={revenue}  /> */}
              {/* <LatestInvoices latestInvoices={latestInvoices} /> */}
            </div>
          </main>
        )
      }
      ```

      In the code above:

      - Page is an `async` component. This allows you to use await to fetch data.
      - There are also 3 components which receive data: `<RevenueChart>`, `<LatestInvoices>` and `<Card>`. They are currently commented out to prevent the application from erroring.

   1. Fetching data for the `<RevenueChart/>` components:

      ```tsx
      import { Card } from "@/app/ui/dashboard/cards"
      import RevenueChart from "@/app/ui/dashboard/revenue-chart"
      import LatestInvoices from "@/app/ui/dashboard/latest-invoices"
      import { lusitana } from "@/app/ui/fonts"
      import { fetchRevenue } from "@/app/lib/data"

      export default async function Page() {
        const revenue = await fetchRevenue()
        // ...
      }
      ```

      Uncomment the `<RevenueChart/>` component, into component file (`/app/ui/dashboard/revenue-chart.tsx`) uncomment the code. Check browser, should be able to see a chart that uses revenue data.

   1. Fetching data for the `<LatestInvoices/>` components:

      ```tsx
      import { fetchRevenue, fetchLatestInvoices } from "@/app/lib/data"

      export default async function Page() {
        const revenue = await fetchRevenue()
        const latestInvoices = await fetchLatestInvoices()
        // ...
      }
      ```

      Uncomment the `<LatestInvoices />` component, into component file (`/app/ui/dashboard/latest-invoices.tsx`) uncomment the code.

   1. Fetch data for the `<Card>` components:

      ```tsx
      import {
        fetchRevenue,
        fetchLatestInvoices,
        fetchCardData,
      } from "@/app/lib/data"

      export default async function Page() {
        const revenue = await fetchRevenue()
        const latestInvoices = await fetchLatestInvoices()
        const {
          numberOfInvoices,
          numberOfCustomers,
          totalPaidInvoices,
          totalPendingInvoices,
        } = await fetchCardData()
        // ...
      }
      ```

      Uncomment the `<Card />` component, into component file (`/app/ui/dashboard/cards.tsx`) uncomment the code.

   However, there are two things need to be aware of:

   - The data requests are unintentionally blocking each other, creating a request _waterfall_.
   - By default, `Next.js` prerenders routes to improve performance, this is called **Static Rendering**. So if the data changes, it won't be reflected in the dashboard.

   1. Parallel data fetching
      A common way to avoid _waterfalls_ is to initiate all data requests at the same time - in parallel.

      In JavaScript can use the `Promise.all()` or `Promise.allSettled()` functions to initiate all promises at the same time. For example, in `data.ts`, can using `Promise.all()` in the `fetchCardData()` function:

      ```ts
      export async function fetchCardData() {
         try {
            const invoiceCountPromise = sql`SELECT COUNT(*) FROM invoices`
            const customerCountPromise = sql`SELECT COUNT(*) FROM customers`
            const invoiceStatusPromise = sql`SELECT
               SUM(CASE WHEN status = 'paid' THEN amount ELSE 0 END) AS "paid",
               SUM(CASE WHEN status = 'pending' THEN amount ELSE 0 END) AS "pending"
               FROM invoices`

            const data = await Promise.all([
               invoiceCountPromise,
               customerCountPromise,
               invoiceStatusPromise,
            ])
            // ...
         }
      }
      ```

1. Static and Dynamic Rendering

   1. Static Rendering

      With static rendering, data fetching and rendering happens on the server at build time (when you deploy) or when revalidating data.

      Whenever a user visits the application, the cached result is served. There are a couple of benefits of static rendering:

      - **Faster Websites** - prerendered content can be cached and globally distributed. This ensures that users around the world can access to website's content more quickly and reliably.
      - **Reduced Server Load** - because the content is cached, the server does not have to dynamically generate content for each user request.
      - **SEO** - prerendered content is easier for search engine crawlers to index, as the content is already available when the page loads. This can lead to improved search engine rankings.

      Static rendering is useful for UI with _no data_ or _data that is shared across users_, such as a static blog post or a product page. It might not be a good fit for a dashboard that has personalized data which is regularly updated.

   1. Dynamic Rendering

      With dynamic rendering, content is rendered on the server for each user at request time (when the user visits the page). There are a couple of benefits of dynamic rendering:

      - **Real-Time Data** - dynamic rendering allows the application to display real-time or frequently updated data. This is ideal for applications where data changes often.
      - **User-Specific Content** - it's easier to serve personalized content, such as dashboards or user profiles, and update the data based on user interaction.
      - **Request Time Information** - dynamic rendering allows to access information that can only be known at request time, such as cookies or the URL search parameters.

1. Streaming

   Streaming is a data transfer technique that allows you to break down a route into smaller "chunks" and progressively stream them from the server to the client as they become ready.

   There are two ways you implement streaming in Next.js:

   - At the page level, with the `loading.tsx` file.
     _(work only by app's link navigate, not by loading directly)_
   - For specific components, with `<Suspense>`.

   > Folders with names in brackets `(`_name_`)` called **Route Group** and excluded from the URL path structure. For example, `/app/dashboard/(overview)/page.tsx` becomes `http://hostname/dashboard` URL.

   Using `loading.tsx` file is streaming whole page.

   _Suspense_ allow to defer rendering parts of the application until some condition is met (e.g. data is loaded).

   Can wrap your dynamic components in `<Suspense>`. Then, pass it a fallback component to show while the dynamic component loads.

   1. Remove from `/app/dashboard/(overview)/page.tsx` file the import of `fetchRevenue` & line with definition `revenue` constant.

   1. Import `<Suspense>` from React, and wrap it around `<RevenueChart />`. You can pass it a fallback component called `<RevenueChartSkeleton>`:

      ```tsx
      import { Suspense } from "react"
      import { RevenueChartSkeleton } from "@/app/ui/skeletons"

      export default async function Page() {
        // const revenue = await fetchRevenue()
        //...
        ;<Suspense fallback={<RevenueChartSkeleton />}>
          <RevenueChart />
        </Suspense>
        //...
      }
      ```

   1. Finally, update the `<RevenueChart>` component to fetch its own data and remove the prop passed to it:

      ```tsx
      import { fetchRevenue } from "@/app/lib/data"
      // ...
      // Make component async, remove the props
      export default async function RevenueChart() {
        const revenue = await fetchRevenue() // Fetch data inside the component
        //...
      }
      ```

   1. Do the same with `<LatestInvoices>` component.

   1. But the `<Card>` component must be wrapped by the `<CardWrapper>` component:

      1. In your page.tsx file:

         Delete the `<Card>` components.
         Delete the `fetchCardData()` function.
         Import a new wrapper component called `<CardWrapper>`.
         Import a new skeleton component called `<CardsSkeleton>`.
         Wrap `<CardWrapper>` in Suspense.

         ```tsx
         import CardWrapper from "@/app/ui/dashboard/cards"
         //...
         import {
           CardsSkeleton,
           LatestInvoicesSkeleton,
           RevenueChartSkeleton,
         } from "@/app/ui/skeletons"
         //...
         export default async function Page() {
           //...
           ;<Suspense fallback={<CardsSkeleton />}>
             <CardWrapper />
           </Suspense>
           //...
         }
         ```

      1. Edit `/app/ui/dashboard/cards.tsx` file:

         ```tsx
         //...
         export default async function CardWrapper() {
           const {
             numberOfInvoices,
             numberOfCustomers,
             totalPaidInvoices,
             totalPendingInvoices,
           } = await fetchCardData()

           return (
             <>
               {/* NOTE: Uncomment this code in Chapter 9 */}
               <Card
                 title="Collected"
                 value={totalPaidInvoices}
                 type="collected"
               />
               <Card
                 title="Pending"
                 value={totalPendingInvoices}
                 type="pending"
               />
               <Card
                 title="Total Invoices"
                 value={numberOfInvoices}
                 type="invoices"
               />
               <Card
                 title="Total Customers"
                 value={numberOfCustomers}
                 type="customers"
               />
             </>
           )
         }
         //...
         ```

1. Partial Prerendering (PPR)

> Partial Prerendering is an experimental feature introduced in Next.js 14.

1.  Implementing Partial Prerendering
    Enable PPR for your Next.js app by adding the ppr option to your `/next.config.mjs` file:

    ```mjs
    /** @type {import('next').NextConfig} */

    const nextConfig = {
      experimental: {
        ppr: "incremental",
      },
    }

    export default nextConfig
    ```

    The `'incremental'` value allows you to adopt PPR for specific routes.

    Next, add the `experimental_ppr` segment config option to the dashboard layout `/app/dashboard/layout.tsx`:

    ```tsx
    import SideNav from "@/app/ui/dashboard/sidenav"

    export const experimental_ppr = true

    // ...
    ```

1.  Adding Search and Pagination

    1.  Fill `/app/dashboard/invoices/page.tsx` file:

        ```tsx
        import Pagination from "@/app/ui/invoices/pagination"
        import Search from "@/app/ui/search"
        import Table from "@/app/ui/invoices/table"
        import { CreateInvoice } from "@/app/ui/invoices/buttons"
        import { lusitana } from "@/app/ui/fonts"
        import { InvoicesTableSkeleton } from "@/app/ui/skeletons"
        import { Suspense } from "react"

        export default async function Page() {
          return (
            <div className="w-full">
              <div className="flex w-full items-center justify-between">
                <h1 className={`${lusitana.className} text-2xl`}>Invoices</h1>
              </div>
              <div className="mt-4 flex items-center justify-between gap-2 md:mt-8">
                <Search placeholder="Search invoices..." />
                <CreateInvoice />
              </div>
              {/* <Suspense key={query + currentPage} fallback={<InvoicesTableSkeleton />}>
                     <Table query={query} currentPage={currentPage} />
                  </Suspense> */}
              <div className="mt-5 flex w-full justify-center">
                {/* <Pagination totalPages={totalPages} /> */}
              </div>
            </div>
          )
        }
        ```

    1.
