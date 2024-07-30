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

    1.  These are the Next.js client hooks that use to implement the search functionality:

        - `useSearchParams` - allows to access the parameters of the current URL. For example, the search params for this URL `/dashboard/invoices?page=1&query=pending` would look like this: `{page: '1', query: 'pending'}`.
        - `usePathname` - lets read the current URL's pathname. For example, for the route `/dashboard/invoices`, `usePathname` would return `'/dashboard/invoices'`.
        - `useRouter` - enables navigation between routes within client components programmatically. There are multiple methods you can use.

    1.  Here's a quick overview of the implementation steps:

        - Capture the user's input.
        - Update the URL with the search params.
        - Keep the URL in sync with the input field.
        - Update the table to reflect the search query.

        1. Capture the user's input

           Go into the `<Search>` Component (`/app/ui/search.tsx`), and will notice:

           - `"use client"` - this is a **Client Component**, which means it can use event listeners and hooks.
           - `<input>` - this is the search input.

           Create a new `handleSearch` function, and add an `onChange` listener to the `<input>` element. `onChange` will invoke `handleSearch` whenever the input value changes:

           ```tsx
           //...
           export default function Search({placeholder}: {placeholder: string}) {
             function handleSearch(term: string) {
               console.log(term)
             }

             return (
               //...
                 <input
                   className="peer block w-full rounded-md border border-gray-200 py-[9px] pl-10 text-sm outline-2 placeholder:text-gray-500"
                   placeholder={placeholder}
                   onChange={(e) => {
                     handleSearch(e.target.value)
                   }}
                 //...
             )
           }
           ```

        1. Update the URL with the search params

           Import the useSearchParams hook from 'next/navigation', and assign it to a variable:

           ```tsx
           //...
           import { useSearchParams } from "next/navigation"

           export default function Search({
             placeholder,
           }: {
             placeholder: string
           }) {
             const searchParams = useSearchParams()
             //...
           }
           ```

           Inside `handleSearch`, create a new `URLSearchParams` instance using your new `searchParams` variable:

           ```tsx
           //...
           function handleSearch(term: string) {
             const params = new URLSearchParams(searchParams)
           }
           //...
           ```

           `URLSearchParams` is a Web API that provides utility methods for manipulating the URL query parameters. Instead of creating a complex string literal, you can use it to get the params string like `?page=1&query=a`.

           `set` the params string based on the user’s input. If the input is empty, you want to delete it:

           ```tsx
           //...
           function handleSearch(term: string) {
             const params = new URLSearchParams(searchParams)
             if (term) {
               params.set("query", term)
             } else {
               params.delete("query")
             }
           }
           //...
           ```

           Now that have the query string. Can use Next.js's `useRouter` and `usePathname` hooks to update the URL.

           Import `useRouter` and `usePathname` from `'next/navigation'`, and use the `replace` method from `useRouter()` inside `handleSearch`:

           ```tsx
           //...
           import { useSearchParams, usePathname, useRouter } from "next/navigation"

           export default function Search({ placeholder }: { placeholder: string }) {
             const searchParams = useSearchParams()
             const pathname = usePathname()
             const { replace } = useRouter()

             function handleSearch(term: string) {
               const params = new URLSearchParams(searchParams)
               if (term) {
                 params.set("query", term)
               } else {
                 params.delete("query")
               }
               replace(`${pathname}?${params.toString()}`)
             }
           //...
           ```

           Here's a breakdown of what's happening:

           - `${pathname}` is the current path, in your case, `"/dashboard/invoices"`.
           - As the user types into the search bar, `params.toString()` translates this input into a URL-friendly format.
           - `replace(${pathname}?${params.toString()})` updates the URL with the user's search data.
             For example, `/dashboard/invoices?query=lee` if the user searches for "Lee".
           - The URL is updated without reloading the page, thanks to Next.js's client-side navigation.

        1. Keeping the URL and input in sync

           To ensure the input field is in sync with the URL and will be populated when sharing, can pass a `defaultValue` to input by reading from `searchParams`:

           ```tsx
           <input
             //...
             defaultValue={searchParams.get("query")?.toString()}
           />
           ```

           > **`defaultValue` vs. `value` (Controlled vs. Uncontrolled)**
           >
           > If are using state to manage the value of an input, would use the `value` attribute to make it a controlled component. This means React would manage the input's state.
           > However, since are not using state, can use `defaultValue`. This means the native input will manage its own state. This is okay since are saving the search query to the URL instead of state.

        1. Updating the table

           Finally, need to update the table component to reflect the search query.

           Navigate back to the invoices page.

           Page components accept a prop called `searchParams`, so you can pass the current URL params to the `<Table>` component (`/app/dashboard/invoices/page.tsx`):

           ```tsx
           //...
           export default async function Page({
             searchParams,
           }: {
             searchParams?: {
               query?: string
               page?: string
             }
           }) {
             const query = searchParams?.query || ""
             const currentPage = Number(searchParams?.page) || 1

             return (
               <div className="w-full">
                 //...
                 <Suspense
                   key={query + currentPage}
                   fallback={<InvoicesTableSkeleton />}
                 >
                   <Table query={query} currentPage={currentPage} />
                 </Suspense>
                 //...
               </div>
             )
           }
           ```

           If navigate to the `<Table>` Component (`/app/ui/invoices/table.tsx`), will see that the two props, `query` and `currentPage`, are passed to the `fetchFilteredInvoices()` function which returns the invoices that match the query:

           ```tsx
           // ...
           export default async function InvoicesTable({
             query,
             currentPage,
           }: {
             query: string
             currentPage: number
           }) {
             const invoices = await fetchFilteredInvoices(query, currentPage)
             // ...
           }
           ```

           With these changes in place, go ahead and test it out. If search for a term, will update the URL, which will send a new request to the server, data will be fetched on the server, and only the invoices that match your query will be returned.

           > **When to use the `useSearchParams()` hook vs. the `searchParams` prop?**
           >
           > Might have noticed used two different ways to extract search params. Whether use one or the other depends on whether are working on the client or the server.
           >
           > `<Search>` is a **Client Component**, so you used the `useSearchParams()` hook to access the params from the client.
           > `<Table>` is a **Server Component** that fetches its own data, so you can pass the `searchParams` prop from the page to the component.
           > As a general rule, if want to read the params from the client, use the `useSearchParams()` hook as this avoids having to go back to the server.

    1.  Best practice: **Debouncing**

        **Debouncing** is a programming practice that limits the rate at which a function can fire. In our case, only want to query the database when the user has stopped typing.

        > **How Debouncing Works:**
        >
        > 1. **Trigger Event**: when an event that should be debounced (like a keystroke in the search box) occurs, a timer starts.
        > 2. **Wait**: if a new event occurs before the timer expires, the timer is reset.
        > 3. **Execution**: if the timer reaches the end of its countdown, the debounced function is executed.

        Can implement debouncing in a few ways, including manually creating your own debounce function. To keep things simple, we'll use a library called `use-debounce`.

        Install `use-debounce`:

            ```bash
            pnpm i use-debounce
            ```

        In `<Search>` Component (`/app/ui/search.tsx`), import a function called `useDebouncedCallback`:

            ```tsx
            // ...
            import { useDebouncedCallback } from 'use-debounce';
            //...
            // Inside the Search Component...
            export default function Search({ placeholder }: { placeholder: string }) {
              const searchParams = useSearchParams()
              const { replace } = useRouter()
              const pathname = usePathname()

              const handleSearch = useDebouncedCallback((term: string) => {
                console.log(`Searching... ${term}`)

                const params = new URLSearchParams(searchParams)

                if (term) {
                  params.set("query", term)
                } else {
                  params.delete("query")
                }
                replace(`${pathname}?${params.toString()}`)
              }, 300)

              return (
                //...
              )
            }
            ```

        This function will wrap the contents of `handleSearch`, and only run the code after a specific time once the user has stopped typing (300ms).

1.  Adding pagination

    The table displays only 6 invoices at a time. This is because the `fetchFilteredInvoices()` function in `/app/lib/data.ts` returns a maximum of 6 invoices per page (`const ITEMS_PER_PAGE = 6`).

    Adding pagination allows users to navigate through the different pages to view all the invoices. Let's see how implement pagination using URL params, just like did it with search.

    Navigate to the `<Pagination/>` component (`/app/ui/invoices/pagination.tsx`) and will notice that it's a **Client Component** (`"use client"`). Don't want to fetch data on the client as this would expose your database secrets (remember, are not using an API layer). Instead, can fetch the data on the server, and pass it to the component as a prop.

    In `/dashboard/invoices/page.tsx`, import a new function called `fetchInvoicesPages` and pass the query from `searchParams` as an argument:

    ```tsx
    import { fetchInvoicesPages } from "@/app/lib/data"

    export default async function Page({
      //..
    }) {
      const query = searchParams?.query || ""
      const currentPage = Number(searchParams?.page) || 1
      const totalPages = await fetchInvoicesPages(query)

      return (
        //...
      )
    }
    ```

    `fetchInvoicesPages` returns the total number of pages based on the search query. For example, if there are 12 invoices that match the search query, and each page displays 6 invoices, then the total number of pages would be 2.

    Next, pass the `totalPages` prop to the `<Pagination/>` component (`/app/dashboard/invoices/page.tsx`):

    ```tsx
    //...
    return (
      <div className="w-full">
        //...
        <div className="mt-5 flex w-full justify-center">
          <Pagination totalPages={totalPages} />
        </div>
      </div>
    )
    //...
    ```

    Navigate to the `<Pagination/>` component (`/app/ui/invoices/pagination.tsx`) and import the `usePathname` and `useSearchParams` hooks. We will use this to get the current page and set the new page. Make sure to also uncomment the code in this component.

    > The application will break temporarily as haven't implemented the `<Pagination/>` logic yet.

    ```tsx
    //...
    import { usePathname, useSearchParams } from "next/navigation"

    export default function Pagination({ totalPages }: { totalPages: number }) {
      const pathname = usePathname()
      const searchParams = useSearchParams()
      const currentPage = Number(searchParams.get("page")) || 1

      // NOTE: Uncomment this code in Chapter 11
      const allPages = generatePagination(currentPage, totalPages)

      //...
    }
    ```

    Next, create a new function inside the `<Pagination>` component (`/app/ui/invoices/pagination.tsx`) called `createPageURL`. Similarly to the search, will use `URLSearchParams` to set the new page number, and pathName to create the URL string:

    ```tsx
    //...
    export default function Pagination({ totalPages }: { totalPages: number }) {
      //...
      // NOTE: Uncomment this code in Chapter 11
      const allPages = generatePagination(currentPage, totalPages)

      const createPageURL = (pageNumber: number | string) => {
        const params = new URLSearchParams(searchParams)
        params.set("page", pageNumber.toString())
        return `${pathname}?${params.toString()}`
      }
      //...
    }
    ```

    Here's a breakdown of what's happening:

    - `createPageURL` creates an instance of the current search parameters.
    - Then, it updates the "page" parameter to the provided page number.
    - Finally, it constructs the full URL using the pathname and updated search parameters.

    > The rest of the `<Pagination>` component deals with styling and different states (first, last, active, disabled, etc). Look through the code to see where `createPageURL` is being called.

    Finally, when the user types a new search query, want to reset the page number to 1. Can do this by updating the `handleSearch` function in the `<Search>` component:

    ```tsx
    //...
    params.set("page", "1")
    //...
    ```

1.  Mutating Data

    **What are Server Actions?**

    **React Server Actions** allow to run asynchronous code directly on the server. They eliminate the need to create API endpoints to mutate the data. Instead, write asynchronous functions that execute on the server and can be invoked from the **Client** or **Server Components**.

    Security is a top priority for web applications, as they can be vulnerable to various threats. This is where **Server Actions** come in. They offer an effective security solution, protecting against different types of attacks, securing the data, and ensuring authorized access. **Server Actions** achieve this through techniques like POST requests, encrypted closures, strict input checks, error message hashing, and host restrictions, all working together to significantly enhance the app's safety.

    1.  Using forms with **Server Actions**

        In React can use the `action` attribute in the `<form>` element to invoke actions. The action will automatically receive the native `FormData` object, containing the captured data. For example:

        ```tsx
        // Server Component
        export default function Page() {
          // Action
          async function create(formData: FormData) {
            "use server"

            // Logic to mutate data...
          }

          // Invoke the action using the "action" attribute
          return <form action={create}>...</form>
        }
        ```

        An advantage of invoking a **Server Action** within a **Server Component** is progressive enhancement - forms work even if JavaScript is disabled on the client.

    1.  Next.js with Server Actions

        **Server Actions** are also deeply integrated with **Next.js caching**. When a form is submitted through a **Server Action**, not only can you use the action to mutate data, but you can also revalidate the associated cache using APIs like `revalidatePath` and `revalidateTag`.

    1.  Creating an invoice

        Here are the steps you'll take to create a new invoice:

        - Create a form to capture the user's input.
        - Create a **Server Action** and invoke it from the form.
        - Inside your **Server Action**, extract the data from the `formData` object.
        - Validate and prepare the data to be inserted into your database.
        - Insert the data and handle any errors.
        - Revalidate the cache and redirect the user back to invoices page.

        1.  Create a new route and form

            To start, inside the `/invoices` folder, add a new route segment called `/create` with a `page.tsx` file. Be using this route to create new invoices:

            ```tsx
            import Form from "@/app/ui/invoices/create-form"
            import Breadcrumbs from "@/app/ui/invoices/breadcrumbs"
            import { fetchCustomers } from "@/app/lib/data"

            export default async function Page() {
              const customers = await fetchCustomers()

              return (
                <main>
                  <Breadcrumbs
                    breadcrumbs={[
                      { label: "Invoices", href: "/dashboard/invoices" },
                      {
                        label: "Create Invoice",
                        href: "/dashboard/invoices/create",
                        active: true,
                      },
                    ]}
                  />
                  <Form customers={customers} />
                </main>
              )
            }
            ```

            This page is a **Server Component** that fetches customers and passes it to the `<Form>` component.

            > The `<Form>` component already was created:
            >
            > Navigate to the `<Form>` component (`/app/ui/invoices/create-form.tsx`), and see that the form:
            >
            > - Has one `<select>` (dropdown) element with a list of **customers**.
            > - Has one `<input>` element for the amount with `type="number"`.
            > - Has two `<input>` elements for the status with `type="radio"`.
            > - Has one button with `type="submit"`.

            On http://localhost:3000/dashboard/invoices/create can see this form.

        1.  Create a Server Action

            Navigate to `lib` directory and create a new file named `actions.ts`. At the top of this file, add the React `use server` directive. This mark all the exported functions within the file as **Server Actions**. These server functions can then be imported and used in **Client** and **Server components**.

            Can also write **Server Actions** directly inside **Server Components** by adding `"use server"` inside the action. But for this project will keep them all organized in a separate file.

            In the `actions.ts` file, create a new async function that accepts `formData`:

            ```ts
            "use server"

            export async function createInvoice(formData: FormData) {}
            ```

            Then, in the `<Form>` component (`/app/ui/invoices/create-form.tsx`), import the `createInvoice` from `actions.ts` file. Add a action attribute to the `<form>` element, and call the `createInvoice` action:

            ```tsx
            //...
            import { createInvoice } from '@/app/lib/actions'

            export default function Form({
              customers,
            }: {
              customers: customerField[]
            }) {
              return (
                <form action={createInvoice}>
                  // ...
              )
            }
            ```

            > **Good to know:**
            >
            > In HTML pass a URL to the `action` attribute. This URL would be the destination where your form data should be submitted (usually an API endpoint).
            >
            > However, in React, the `action` attribute is considered a special prop - meaning React builds on top of it to allow actions to be invoked.
            >
            > Behind the scenes, **Server Actions** create a `POST` API endpoint. This is why you don't need to create API endpoints manually when using Server Actions.

        1.  Extract the data from `formData`

            In the `actions.ts` file will need to extract the values of `formData`, there are a couple of methods can be used. For this example, let's use the `.get(name)` method:

            ```ts
            "use server"

            export async function createInvoice(formData: FormData) {
              const rawFormData = {
                customerId: formData.get("customerId"),
                amount: formData.get("amount"),
                status: formData.get("status"),
              }
              // Test it out:
              console.log(rawFormData)
            }
            ```

            > **Tip:**
            >
            > If you're working with forms that have many fields, you may want to consider using the `entries()` method with JavaScript's `Object.fromEntries()`. For example:
            >
            > `const rawFormData = Object.fromEntries(formData.entries())`

        1.  Validate and prepare the data

            Before sending the form data to the database, want to ensure it's in the correct format and with the correct types. Invoices table expects data in the following format (`/app/lib/definitions.ts`):

            ```ts
            export type Invoice = {
              id: string // Will be created on the database
              customer_id: string
              amount: number // Stored in cents
              status: "pending" | "paid"
              date: string
            }
            ```

            So far, only have the `customer_id`, `amount`, and `status` from the form.

            1. Type validation and coercion

               It's important to validate that the data from form aligns with the expected types in your database. For instance, if add a `console.log` inside the action:

               ```ts
               console.log(typeof rawFormData.amount)
               ```

               > **Notice** that `amount` is of type `string` and not `number`. This is because input elements with `type="number"` actually return a string, not a number!

               To handle type validation, have a few options. While can manually validate types, using a type validation library can save you time and effort. For your example, we'll use [**Zod**](https://zod.dev/), a TypeScript-first validation library that can simplify this task for you.

               In the `actions.ts` file, import **Zod** and define a schema that matches the shape of the form object. This schema will validate the `formData` before saving it to a database.

               The `amount` field is specifically set to coerce (change) from a string to a number while also validating its type.

               Then pass the `rawFormData` to `CreateInvoice` to validate the types:

               ```ts
               "use server"

               import { z } from "zod"

               const FormSchema = z.object({
                 id: z.string(),
                 customerId: z.string(),
                 amount: z.coerce.number(),
                 status: z.enum(["pending", "paid"]),
                 date: z.string(),
               })

               const CreateInvoice = FormSchema.omit({ id: true, date: true })

               export async function createInvoice(formData: FormData) {
                 const { customerId, amount, status } = CreateInvoice.parse({
                   customerId: formData.get("customerId"),
                   amount: formData.get("amount"),
                   status: formData.get("status"),
                 })
               }
               ```

            1. Storing values in cents

               It's usually good practice to store monetary values in cents in your database to eliminate JavaScript floating-point errors and ensure greater accuracy.

               Let's convert the amount into cents (`/app/lib/actions.ts`):

               ```ts
               const amountInCents = amount * 100
               ```

            1. Creating new dates

               Finally, let's create a new date with the format "YYYY-MM-DD" for the invoice's creation date (`/app/lib/actions.ts`):

               ```ts
               const date = new Date().toISOString().split("T")[0]
               ```

        1.  Inserting the data into your database

            Now that have all the values needed for the database, can create an SQL query to insert the new invoice into the database and pass in the variables:

            ```ts
            //...
            import { sql } from "@vercel/postgres"
            //...
            await sql`
              INSERT INTO invoices (customer_id, amount, status, date)
              VALUES (${customerId}, ${amountInCents}, ${status}, ${date})
            `
            //..
            ```

        1.  Revalidate and redirect

            Next.js has a **Client-side Router Cache** that stores the route segments in the user's browser for a time. Along with **prefetching**, this cache ensures that users can quickly navigate between routes while reducing the number of requests made to the server.

            Since are updating the data displayed in the invoices route, wants to clear this cache and trigger a new request to the server. Can do this with the `revalidatePath` function from Next.js (`/app/lib/actions.ts`):

            ```ts
            //...
            import { revalidatePath } from "next/cache"
            //...
            revalidatePath("/dashboard/invoices")
            //...
            ```

            Once the database has been updated, the `/dashboard/invoices` path will be revalidated, and fresh data will be fetched from the server.

            At this point, also wants to redirect the user back to the `/dashboard/invoices` page. Сan do this with the `redirect` function from Next.js:

            ```ts
            //...
            import { redirect } from "next/navigation"
            //...
            redirect("/dashboard/invoices")
            //...
            ```

    1.  Updating an invoice

        The updating invoice form is similar to the create an invoice form, except will need to pass the invoice `id` to update the record in the database. Let's see how can get and pass the invoice `id`.

        These are the steps you'll take to update an invoice:

        - Create a new dynamic route segment with the invoice `id`.
        - Read the invoice `id` from the page params.
        - Fetch the specific invoice from your database.
        - Pre-populate the form with the invoice data.
        - Update the invoice data in your database.

          1. Create a Dynamic Route Segment with the invoice `id`

             Next.js allows to create **Dynamic Route Segments** when don't know the exact segment name and want to create routes based on data. This could be blog post titles, product pages, etc. Can create dynamic route segments by wrapping a folder's name in square brackets. For example, `[id]`, `[post]` or `[slug]`.

             In the `/invoices` folder, create a new dynamic route called `[id]`, then a new route called `edit` with a `page.tsx` file.

             In `<Table>` component (`/app/ui/invoices/table.tsx`), notice there's a `<UpdateInvoice />` button that receives the invoice's `id` from the table records:

             ```tsx
             //...
             <UpdateInvoice id={invoice.id} />
             //...
             ```

             Navigate to the `<UpdateInvoice />` component (`/app/ui/invoices/buttons.tsx`), and update the `href` of the `Link` to accept the `id` prop. Can use template literals to link to a dynamic route segment:

             ```tsx
             export function UpdateInvoice({ id }: { id: string }) {
               return (
                 <Link
                   href={`/dashboard/invoices/${id}/edit`}
                   className="rounded-md border p-2 hover:bg-gray-100"
                 >
                   <PencilIcon className="w-5" />
                 </Link>
               )
             }
             ```

          1. Read the invoice `id` from page `params`

             Back on the `<Page>` component (`/app/dashboard/invoices/[id]/edit/page.tsx`) and paste the following code:

             ```tsx
             import Form from "@/app/ui/invoices/edit-form"
             import Breadcrumbs from "@/app/ui/invoices/breadcrumbs"
             import { fetchInvoiceById, fetchCustomers } from "@/app/lib/data"

             export default async function Page() {
               return (
                 <main>
                   <Breadcrumbs
                     breadcrumbs={[
                       { label: "Invoices", href: "/dashboard/invoices" },
                       {
                         label: "Edit Invoice",
                         href: `/dashboard/invoices/${id}/edit`,
                         active: true,
                       },
                     ]}
                   />
                   <Form invoice={invoice} customers={customers} />
                 </main>
               )
             }
             ```

             Notice how it's similar to the `/create` invoice page, except it imports a different form (from the `edit-form.tsx` file). This form should be **pre-populated** with a `defaultValue` for the customer's name, invoice amount, and status. To pre-populate the form fields, you need to fetch the specific invoice using `id`.

             In addition to `searchParams`, page components also accept a prop called `params` which you can use to access the `id`. Update the `<Page>` component (`/app/dashboard/invoices/[id]/edit/page.tsx`) to receive the prop:

             ```tsx
             //...
             export default async function Page({
               params,
             }: {
               params: { id: string }
             }) {
               const id = params.id
               // ...
             }
             ```

          1. Fetch the specific invoice

             Then:

             - Import a new function called `fetchInvoiceById` and pass the id as an argument.
             - Import `fetchCustomers` to fetch the customer names for the dropdown.

             Can use `Promise.all` to fetch both the invoice and customers in parallel:

             ```tsx
             //...
             const [invoice, customers] = await Promise.all([
               fetchInvoiceById(id),
               fetchCustomers(),
             ])
             //...
             ```

          1. Pass the `id` to the Server Action

             Lastly, wanted to pass the `id` to the **Server Action** so can update the right record in your database. **Cannot** pass the `id` as an argument like so:

             ```tsx
             // Passing an id as argument won't work
             <form action={updateInvoice(id)}>
             ```

             Instead, can pass `id` to the Server Action using JS `bind`. This will ensure that any values passed to the **Server Action** are encoded (`/app/ui/invoices/edit-form.tsx`):

             ```tsx
             //...
             import { updateInvoice } from "@/app/lib/actions"

             export default function EditInvoiceForm({
               invoice,
               customers,
             }: {
               invoice: InvoiceForm
               customers: CustomerField[]
             }) {
               const updateInvoiceWithId = updateInvoice.bind(null, invoice.id)
               return (
                 <form action={updateInvoiceWithId}>
                   <div className="rounded-md bg-gray-50 p-4 md:p-6">
                   //...
                 </form>
               )
             }
             ```

             > **Note:**
             >
             > Using a hidden input field in your form also works (e.g. `<input type="hidden" name="id" value={invoice.id} />`).
             > However, the values will appear as full text in the HTML source, which is not ideal for sensitive data like IDs.

             Then, in the `/app/lib/actions.ts` file, create a new action `updateInvoice`:

             ```ts
             // Use Zod to update the expected types
             const UpdateInvoice = FormSchema.omit({ id: true, date: true })
             // ...
             export async function updateInvoice(
               id: string,
               formData: FormData
             ) {
               const { customerId, amount, status } = UpdateInvoice.parse({
                 customerId: formData.get("customerId"),
                 amount: formData.get("amount"),
                 status: formData.get("status"),
               })

               const amountInCents = amount * 100

               await sql`
                  UPDATE invoices
                  SET customer_id = ${customerId}, amount = ${amountInCents}, status = ${status}
                  WHERE id = ${id}
                `

               revalidatePath("/dashboard/invoices")
               redirect("/dashboard/invoices")
             }
             ```

             Similarly to the `createInvoice` action, here are:

             - Extracting the data from `formData`.
             - Validating the types with **Zod**.
             - Converting the `amount` to cents.
             - Passing the variables to your SQL query.
             - Calling `revalidatePath` to clear the client cache and make a new server request.
             - Calling `redirect` to redirect the user to the invoice's page.

    1.  Deleting an invoice

        To delete an invoice using a **Server Action**, wrap the delete button in a `<form>` element and pass the `id` to the **Server Action** using `bind` (`/app/ui/invoices/buttons.tsx`):

        ```tsx
        //...
        import { deleteInvoice } from "@/app/lib/actions"
        //...
        export function DeleteInvoice({ id }: { id: string }) {
          const deleteInvoiceWithId = deleteInvoice.bind(null, id)

          return (
            <form action={deleteInvoiceWithId}>
            //...
          )
        }
        ```

        Inside the `/app/lib/actions.ts` file, create a new action called `deleteInvoice`:

        ```ts
        //...
        export async function deleteInvoice(id: string) {
          await sql`DELETE FROM invoices WHERE id = ${id}`
          revalidatePath("/dashboard/invoices")
        }
        ```

        Since this action is being called in the `/dashboard/invoices` path, don't need to call `redirect`. Calling `revalidatePath` will trigger a new server request and re-render the table.

1.  Handling Errors

    1. Adding `try/catch` to **Server Actions** (`/app/lib/actions.ts`)

       ```ts
       //...
       export async function createInvoice(formData: FormData) {
         //..
         try {
           await sql`
            INSERT INTO invoices (customer_id, amount, status, date)
            VALUES (${customerId}, ${amountInCents}, ${status}, ${date})
          `
         } catch (error) {
           return {
             message: "Database Error: Failed to Create Invoice.",
           }
         }
         //...
       }

       export async function updateInvoice(id: string, formData: FormData) {
         //...
         try {
           await sql`
              UPDATE invoices
              SET customer_id = ${customerId}, amount = ${amountInCents}, status = ${status}
              WHERE id = ${id}
            `
         } catch (error) {
           return { message: "Database Error: Failed to Update Invoice." }
         }
         //...
       }

       export async function deleteInvoice(id: string) {
         try {
           await sql`DELETE FROM invoices WHERE id = ${id}`
         } catch (error) {
           return { message: "Database Error: Failed to Delete Invoice." }
         }

         revalidatePath("/dashboard/invoices")
       }
       ```

    1. Handling all errors with `error.tsx`

       The `error.tsx` file can be used to define a UI boundary for a route segment. It serves as a **catch-all** for unexpected errors and allows you to display a fallback UI to your users.

       Inside the `/dashboard/invoices` folder, create a new file called `error.tsx` and paste the following code:

       ```tsx
       "use client"

       import { useEffect } from "react"

       export default function Error({
         error,
         reset,
       }: {
         error: Error & { digest?: string }
         reset: () => void
       }) {
         useEffect(() => {
           // Optionally log the error to an error reporting service
           console.error(error)
         }, [error])

         return (
           <main className="flex h-full flex-col items-center justify-center">
             <h2 className="text-center">Something went wrong!</h2>
             <button
               className="mt-4 rounded-md bg-blue-500 px-4 py-2 text-sm text-white transition-colors hover:bg-blue-400"
               onClick={
                 // Attempt to recover by trying to re-render the invoices route
                 () => reset()
               }
             >
               Try again
             </button>
           </main>
         )
       }
       ```

       There are a few things you'll notice about the code above:

       - `"use client"` - `error.tsx` needs to be a **Client Component**.
       - It accepts two props:

         - `error`: This object is an instance of JavaScript's native **Error** object.
         - `reset`: This is a function to reset the error boundary. When executed, the function will try to re-render the route segment.

    1. Handling 404 errors with the `notFound` function

       Another way can handle errors gracefully is by using the `notFound` function. While `error.tsx` is useful for catching all errors, `notFound` can be used when trying to fetch a resource that doesn't exist.

       For example, try edit invoice by fake UUID that doesn't exist in the database [http://localhost:3000/dashboard/invoices/2e94d1ed-d220-449f-9f11-f0bbceed9645/edit](http://localhost:3000/dashboard/invoices/2e94d1ed-d220-449f-9f11-f0bbceed9645/edit).

       Will immediately see `error.tsx` kicks in because this is a child route of `/invoices` where `error.tsx` is defined.

       However, if necessary to be more specific, can show a 404 error to tell the user the resource they're trying to access hasn't been found.

       Now that known the invoice doesn't exist in the database, let's use `notFound` to handle it. Navigate to `/dashboard/invoices/[id]/edit/page.tsx`, and `import { notFound } from 'next/navigation'`.

       Then can use a conditional to invoke `notFound` if the invoice doesn't exist:

       ```tsx
       //...
       import { notFound } from "next/navigation"

       export default async function Page({
         params,
       }: {
         params: { id: string }
       }) {
         const id = params.id
         const [invoice, customers] = await Promise.all([
           fetchInvoiceById(id),
           fetchCustomers(),
         ])

         if (!invoice) {
           notFound()
         }
         //...
       }
       ```

       `<Page>` will now throw an error if a specific invoice is not found. To show an error UI to the user. Create a `not-found.tsx` file inside the `/edit` folder:

       ```tsx
       import Link from "next/link"
       import { FaceFrownIcon } from "@heroicons/react/24/outline"

       export default function NotFound() {
         return (
           <main className="flex h-full flex-col items-center justify-center gap-2">
             <FaceFrownIcon className="w-10 text-gray-400" />
             <h2 className="text-xl font-semibold">404 Not Found</h2>
             <p>Could not find the requested invoice.</p>
             <Link
               href="/dashboard/invoices"
               className="mt-4 rounded-md bg-blue-500 px-4 py-2 text-sm text-white transition-colors hover:bg-blue-400"
             >
               Go Back
             </Link>
           </main>
         )
       }
       ```

1.  Improving Accessibility

    Accessibility refers to designing and implementing web applications that everyone can use, including those with disabilities. It's a vast topic that covers many areas, such as keyboard navigation, semantic HTML, images, colors, videos, etc.

    1. Using the ESLint accessibility plugin in Next.js

       Next.js includes the `eslint-plugin-jsx-a11y` plugin in its ESLint config to help catch accessibility issues early. For example, this plugin warns if you have images without `alt` text, use the `aria-\*` and `role` attributes incorrectly, and more.

       Optionally, if would like to try this out, add `next lint` as a script in the `package.json` file:

       ```json
       {
         //...
         "scripts": {
           "build": "next build",
           "dev": "next dev",
           "start": "next start",
           "lint": "next lint"
         }
         //...
       }
       ```

       Then run `pnpm lint` in the terminal.

    1. Form validation

       1. Client-Side validation

          There are a couple of ways to validate forms on the client. The simplest would be to rely on the form validation provided by the browser by adding the `required` attribute to the `<input>` and `<select>` elements in your forms. For example:

          ```tsx title="/app/ui/invoices/create-form.tsx"
          <input
            id="amount"
            name="amount"
            type="number"
            placeholder="Enter USD amount"
            className="peer block w-full rounded-md border border-gray-200 py-2 pl-10 text-sm outline-2 placeholder:text-gray-500"
            required
          />
          ```

       1. Server-Side validation

          By validating forms on the server, you can:

          - Ensure your data is in the expected format before sending it to your database.
          - Reduce the risk of malicious users bypassing client-side validation.
          - Have one source of truth for what is considered _valid_ data.

          In the `create-form.tsx` component, import the `useActionState` hook from react. Since `useActionState` is a hook, will need to turn the form into a **Client Component** using `"use client"` directive:

          ```tsx title="/app/ui/invoices/create-form.tsx"
          "use client"
          //...
          import { useActionState } from "react"
          //...
          ```

          Inside the Form Component, the `useActionState` hook:

          - Takes two arguments: `(action, initialState)`.
          - Returns two values: `[state, formAction]` - the form state, and a function to be called when the form is submitted.

          Pass the `createInvoice` action as an argument of `useActionState`, and inside the `<form action={}>` attribute, call `formAction`:

          ```tsx title="/app/ui/invoices/create-form.tsx"
          //...
          import { useActionState } from "react"

          export default function Form({
            customers,
          }: {
            customers: CustomerField[]
          }) {
            const [state, formAction] = useActionState(
              createInvoice,
              initialState
            )

            return <form action={formAction}>...</form>
          }
          ```

          The `initialState` can be anything defined, in this case, create an object with two empty keys: `message` and `errors`, and import the `State` type from your `actions.ts` file:

          ```tsx title="/app/ui/invoices/create-form.tsx"
          //...
          import { createInvoice, State } from "@/app/lib/actions"
          import { useActionState } from "react"

          export default function Form({
            customers,
          }: {
            customers: CustomerField[]
          }) {
            const initialState: State = { message: null, errors: {} }
            const [state, formAction] = useActionState(
              createInvoice,
              initialState
            )

            return <form action={formAction}>...</form>
          }
          ```

          This may seem confusing initially, but it'll make more sense once you update the server action.

          In the `action.ts` file, can use **Zod** to validate form data. Update the `FormSchema` as follows:

          ```ts title="/app/lib/actions.ts"
          const FormSchema = z.object({
            id: z.string(),
            customerId: z.string({
              invalid_type_error: "Please select a customer.",
            }),
            amount: z.coerce
              .number()
              .gt(0, { message: "Please enter an amount greater than $0." }),
            status: z.enum(["pending", "paid"], {
              invalid_type_error: "Please select an invoice status.",
            }),
            date: z.string(),
          })
          ```

          - `customerId` - Zod already throws an error if the customer field is empty as it expects a type `string`. But let's add a friendly message if the user doesn't select a customer.
          - `amount` - Since are coercing the amount type from `string` to `number`, it'll default to zero if the string is empty. Let's tell Zod we always want the amount greater than 0 with the `.gt()` function.
          - `status` - Zod already throws an error if the status field is empty as it expects "pending" or "paid". Let's also add a friendly message if the user doesn't select a status.

          Next, update the `createInvoice` action to accept two parameters - `prevState` and `formData`:

          ```ts title="app/lib/actions.ts"
          export type State = {
            errors?: {
              customerId?: string[]
              amount?: string[]
              status?: string[]
            }
            message?: string | null
          }

          export async function createInvoice(
            prevState: State,
            formData: FormData
          ) {
            //...
          }
          ```

          - `formData` - same as before.
          - `prevState` - contains the state passed from the `useActionState` hook. It don't using in the action for now, but it's a required prop.

          Then, change the **Zod** `parse()` function to `safeParse()`:

          ```ts title="/app/lib/actions.ts"
          export async function createInvoice(
            prevState: State,
            formData: FormData
          ) {
            // Validate form fields using Zod
            const validatedFields = CreateInvoice.safeParse({
              customerId: formData.get("customerId"),
              amount: formData.get("amount"),
              status: formData.get("status"),
            })
            // ...
          }
          ```

          > `safeParse()` will return an object containing either a `success` or `error` field. This will help handle validation more gracefully without having put this logic inside the `try/catch` block.

          Before sending the information to the database, check if the form fields were validated correctly with a conditional:

          ```ts title="/app/lib/actions.ts"
          export async function createInvoice(
            prevState: State,
            formData: FormData
          ) {
            // Validate form fields using Zod
            const validatedFields = CreateInvoice.safeParse({
              customerId: formData.get("customerId"),
              amount: formData.get("amount"),
              status: formData.get("status"),
            })

            // If form validation fails, return errors early. Otherwise, continue.
            if (!validatedFields.success) {
              return {
                errors: validatedFields.error.flatten().fieldErrors,
                message: "Missing Fields. Failed to Create Invoice.",
              }
            }
            // ...
          }
          ```

          If `validatedFields` isn't successful, we return the function early with the error messages from **Zod**.

          Finally, since are handling form validation separately, outside the `try/catch` block, can return a specific message for any database errors.

          Now let's display the errors in the form component. Back in the `create-form.tsx` component, can access the errors using the form `state`.

          Add a **ternary operator** that checks for each specific error. For example, after the customer's field, you can add:

          ```tsx title="/app/ui/invoices/create-form.tsx"
          <form action={formAction}>
            //...
            <select
              id="customer"
              name="customerId"
              className="peer block w-full rounded-md border border-gray-200 py-2 pl-10 text-sm outline-2 placeholder:text-gray-500"
              defaultValue=""
              aria-describedby="customer-error"
            >
            //...
            <div id="customer-error" aria-live="polite" aria-atomic="true">
              {state.errors?.customerId &&
                state.errors.customerId.map((error: string) => (
                  <p className="mt-2 text-sm text-red-500" key={error}>
                    {error}
                  </p>
                ))}
            </div>
            // ...
          </form>
          ```

          In the code above adding the following aria labels:

          - `aria-describedby="customer-error"`: This establishes a relationship between the `select` element and the error message container. It indicates that the container with `id="customer-error"` describes the `select` element. Screen readers will read this description when the user interacts with the `select` box to notify them of errors.
          - `id="customer-error"`: This `id` attribute uniquely identifies the HTML element that holds the error message for the `select` input. This is necessary for `aria-describedby` to establish the relationship.
          - `aria-live="polite"`: The screen reader should politely notify the user when the error inside the `div` is updated. When the content changes (e.g. when a user corrects an error), the screen reader will announce these changes, but only when the user is idle so as not to interrupt them.

          Do the same with **Invoice Amount** & **Invoice Status** form's fields of `create-form.tsx` component.

          And the same with `edit-form.tsx` component:

          - Add `useActionState` to the `edit-form.tsx` component.
          - Edit the `updateInvoice` action to handle validation errors from **Zod**.
          - Display the errors in the component, and add aria labels to improve accessibility.

1.  Adding Authentication

    A secure website often uses multiple ways to check a user's identity. For instance, after entering the username and password, the site may send a verification code to the user's device or use an external app like **Google Authenticator**. This 2-factor authentication (2FA) helps increase security. Even if someone learns a password, they can't access to account without the unique token.

    1. Authentication vs. Authorization

       In web development, authentication and authorization serve different roles:

       - **Authentication** is about making sure the user is who they say they are. Are proving user's identity with something user have like a username and password.
       - **Authorization** is the next step. Once a user's identity is confirmed, authorization decides what parts of the application they are allowed to use.

       So, authentication checks who user are, and authorization determines what user can do or access in the application.

    1. Creating the login route

       Start by creating a new route in your application called `/login` and paste the following code (`/app/login/page.tsx`):

       ```tsx title="/app/login/page.tsx"
       import AcmeLogo from "@/app/ui/acme-logo"
       import LoginForm from "@/app/ui/login-form"

       export default function LoginPage() {
         return (
           <main className="flex items-center justify-center md:h-screen">
             <div className="relative mx-auto flex w-full max-w-[400px] flex-col space-y-2.5 p-4 md:-mt-32">
               <div className="flex h-20 w-full items-end rounded-lg bg-blue-500 p-3 md:h-36">
                 <div className="w-32 text-white md:w-36">
                   <AcmeLogo />
                 </div>
               </div>
               <LoginForm />
             </div>
           </main>
         )
       }
       ```

       Notice the page imports `<LoginForm />`, which will update later.

    1. NextAuth.js

       We will be using **NextAuth.js** to add authentication to your application. NextAuth.js abstracts away much of the complexity involved in managing sessions, sign-in and sign-out, and other aspects of authentication. While you can manually implement these features, the process can be time-consuming and error-prone. NextAuth.js simplifies the process, providing a unified solution for auth in Next.js applications.

       1. Setting up NextAuth.js

          Install NextAuth.js by running the following command in your terminal:

          ```bash
          pnpm i next-auth
          ```

          Next, generate a secret key for the application. This key is used to encrypt cookies, ensuring the security of user sessions. Can do this by running the following command in your terminal:

          ```bash
          openssl rand -base64 32
          ```

          Then, in the `.env` file, add generated key to the `AUTH_SECRET` variable:

          ```env
          AUTH_SECRET=your-secret-key
          ```

          > For auth to work in production, will need to update the environment variables in the Vercel project too.
          >
          > Check out this [guide](https://vercel.com/docs/projects/environment-variables) on how to add environment variables on Vercel.

       1. Adding the pages option

          Create an `auth.config.ts` file at the root of our project that exports an `authConfig` object. This object will contain the configuration options for NextAuth.js. For now, it will only contain the `pages` option:

          ```ts title="/auth.config.ts"
          import type { NextAuthConfig } from "next-auth"

          export const authConfig = {
            pages: {
              signIn: "/login",
            },
          }
          ```

          You can use the `pages` option to specify the route for custom sign-in, sign-out, and error pages. This is not required, but by adding `signIn: '/login'` into our pages option, the user will be redirected to our custom login page, rather than the NextAuth.js default page.

    1. Protecting your routes with Next.js Middleware

       Next, add the logic to protect your routes. This will prevent users from accessing the dashboard pages unless they are logged in (`/auth.config.ts`):

       ```ts title="/auth.config.ts"
       import type { NextAuthConfig } from "next-auth"

       export const authConfig = {
         pages: {
           signIn: "/login",
         },
         callbacks: {
           authorized({ auth, request: { nextUrl } }) {
             const isLoggedIn = !!auth?.user
             const isOnDashboard = nextUrl.pathname.startsWith("/dashboard")
             if (isOnDashboard) {
               if (isLoggedIn) return true
               return false // Redirect unauthenticated users to login page
             } else if (isLoggedIn) {
               return Response.redirect(new URL("/dashboard", nextUrl))
             }
             return true
           },
         },
         providers: [], // Add providers with an empty array for now
       } satisfies NextAuthConfig
       ```

       The `authorized` callback is used to verify if the request is authorized to access a page via **Next.js Middleware**. It is called before a request is completed, and it receives an object with the `auth` and `request` properties. The `auth` property contains the user's session, and the `request` property contains the incoming request.

       The `providers` option is an array where you list different login options. For now, it's an empty array to satisfy NextAuth config. You'll learn more about it in the [Adding the Credentials provider](https://nextjs.org/learn/dashboard-app/adding-authentication#adding-the-credentials-provider) section.

       Next, you will need to import the `authConfig` object into a Middleware file. In the root of your project, create a file called `middleware.ts` and paste the following code:

       ```ts title="middleware.ts"
       import NextAuth from "next-auth"
       import { authConfig } from "./auth.config"

       export default NextAuth(authConfig).auth

       export const config = {
         // https://nextjs.org/docs/app/building-your-application/routing/middleware#matcher
         matcher: ["/((?!api|_next/static|_next/image|.*\\.png$).*)"],
       }
       ```

       Here you're initializing NextAuth.js with the `authConfig` object and exporting the `auth` property. You're also using the `matcher` option from Middleware to specify that it should run on specific paths.

       The advantage of employing Middleware for this task is that the protected routes will not even start rendering until the Middleware verifies the authentication, enhancing both the security and performance of your application.

    1. Password hashing

       > We'll need to create a separate file for the `bcrypt` package.
       >
       > This is because `bcrypt` relies on Node.js APIs not available in Next.js Middleware.

       Create a new file called `/auth.ts` that spreads your `authConfig` object:

       ```ts title="/auth.ts"
       import NextAuth from "next-auth"
       import { authConfig } from "./auth.config"

       export const { auth, signIn, signOut } = NextAuth({
         ...authConfig,
       })
       ```

    1. Adding the Credentials provider

       Next, we'll need to add the `providers` option for NextAuth.js. `providers` is an array where you list different login options such as Google or GitHub. For this course, we will focus on using the [Credentials provider](https://authjs.dev/getting-started/providers/credentials-tutorial) only.

       The Credentials provider allows users to log in with a username and a password.

       ```ts title="/auth.ts"
       import NextAuth from "next-auth"
       import { authConfig } from "./auth.config"
       import Credentials from "next-auth/providers/credentials"

       export const { auth, signIn, signOut } = NextAuth({
         ...authConfig,
         providers: [Credentials({})],
       })
       ```

       > **Good to know:**
       >
       > Although we're using the Credentials provider, it's generally recommended to use alternative providers such as [OAuth](https://authjs.dev/getting-started/providers/oauth-tutorial) or [email](https://authjs.dev/getting-started/providers/email-tutorial) providers. See the [NextAuth.js docs](https://authjs.dev/getting-started/providers) for a full list of options.

    1. Adding the sign in functionality

       Use the `authorize` function to handle the authentication logic. Similarly to Server Actions, can use `zod` to validate the email and password before checking if the user exists in the database.

       After validating the credentials, create a new `getUser` function that queries the user from the database.

       Then, call `bcrypt.compare` to check if the passwords match.

       Finally, if the passwords match you want to return the user, otherwise, return `null` to prevent the user from logging in:

       ```ts title="/auth.ts"
       import NextAuth from "next-auth"
       import { authConfig } from "./auth.config"
       import Credentials from "next-auth/providers/credentials"
       import { z } from "zod"
       import { sql } from "@vercel/postgres"
       import type { User } from "@/app/lib/definitions"
       import bcrypt from "bcrypt"

       async function getUser(email: string): Promise<User | undefined> {
         try {
           const user =
             await sql<User>`SELECT * FROM users WHERE email=${email}`
           return user.rows[0]
         } catch (error) {
           console.error("Failed to fetch user:", error)
           throw new Error("Failed to fetch user.")
         }
       }

       export const { auth, signIn, signOut } = NextAuth({
         ...authConfig,
         providers: [
           Credentials({
             async authorize(credentials) {
               const parsedCredentials = z
                 .object({
                   email: z.string().email(),
                   password: z.string().min(6),
                 })
                 .safeParse(credentials)

               if (parsedCredentials.success) {
                 const { email, password } = parsedCredentials.data
                 const user = await getUser(email)
                 if (!user) return null
                 const passwordsMatch = await bcrypt.compare(
                   password,
                   user.password
                 )

                 if (passwordsMatch) return user
               }

               console.log("Invalid credentials")
               return null
             },
           }),
         ],
       })
       ```

    1. Updating the login form

       Now we need to connect the auth logic with your login form. In your `actions.ts` file, create a new action called `authenticate`. This action should import the `signIn` function from `auth.ts`:

       ```ts title="/app/lib/actions.ts"
       "use server"

       import { signIn } from "@/auth"
       import { AuthError } from "next-auth"
       // ...
       export async function authenticate(
         prevState: string | undefined,
         formData: FormData
       ) {
         try {
           await signIn("credentials", formData)
         } catch (error) {
           if (error instanceof AuthError) {
             switch (error.type) {
               case "CredentialsSignin":
                 return "Invalid credentials."
               default:
                 return "Something went wrong."
             }
           }
           throw error
         }
       }
       ```

       If there's a `'CredentialsSignin'` error, we want to show an appropriate error message. Can learn about NextAuth.js errors in [the documentation](https://errors.authjs.dev/).

       Finally, in the `login-form.tsx` component, can use React's `useActionState` to call the server action, handle form errors, and display the form's pending state (`app/ui/login-form.tsx`):

       ```tsx title="app/ui/login-form.tsx"
       //...
       import { useActionState } from "react"
       import { authenticate } from "@/app/lib/actions"

       export default function LoginForm() {
         const [errorMessage, formAction, isPending] = useActionState(
           authenticate,
           undefined
         )

         return (
           <form action={formAction} className="space-y-3">
             //...
               <Button className="mt-4 w-full" aria-disabled={isPending}>
                 Log in{" "}
                 <ArrowRightIcon className="ml-auto h-5 w-5 text-gray-50" />
               </Button>
               <div
                 className="flex h-8 items-end space-x-1"
                 aria-live="polite"
                 aria-atomic="true"
               >
                 {errorMessage && (
                   <>
                     <ExclamationCircleIcon className="h-5 w-5 text-red-500" />
                     <p className="text-sm text-red-500">{errorMessage}</p>
                   </>
                 )}
               </div>
             </div>
           </form>
         )
       }
       ```

    1. Adding the logout functionality

       To add the logout functionality to `<SideNav />`, call the `signOut` function from `auth.ts` in the `<form>` element (`/ui/dashboard/sidenav.tsx`):

       ```tsx title="/ui/dashboard/sidenav.tsx"
       //...
       import { signOut } from "@/auth"

       export default function SideNav() {
         return (
           //...
           <form
             action={async () => {
               "use server"
               await signOut()
             }}
           >
             //...
           </form>
           //...
         )
       }
       ```

    1. Try it out
       Now, try it out. You should be able to log in and out of your application using the following credentials:

       - **e-mail:** `user@nextmail.com`
       - **password:** `123456`

1.  Adding Metadata

    Metadata is crucial for SEO and shareability. In this chapter, we'll discuss how you can add metadata to your Next.js application.

    1. What is metadata?

       In web development, metadata provides additional details about a webpage. Metadata is not visible to the users visiting the page. Instead, it works behind the scenes, embedded within the page's HTML, usually within the `<head>` element. This hidden information is crucial for search engines and other systems that need to understand your webpage's content better.

    1. Why is metadata important?

       Metadata plays a significant role in enhancing a webpage's SEO, making it more accessible and understandable for search engines and social media platforms. Proper metadata helps search engines effectively index webpages, improving their ranking in search results. Additionally, metadata like Open Graph improves the appearance of shared links on social media, making the content more appealing and informative for users.

    1. Types of metadata

       There are various types of metadata, each serving a unique purpose. Some common types include:

       - **Title Metadata**: Responsible for the title of a webpage that is displayed on the browser tab. It's crucial for SEO as it helps search engines understand what the webpage is about.

         ```html
         <title>Page Title</title>
         ```

       - **Description Metadata**: This metadata provides a brief overview of the webpage content and is often displayed in search engine results.

         ```html
         <meta
           name="description"
           content="A brief description of the page content."
         />
         ```

       - **Keyword Metadata**: This metadata includes the keywords related to the webpage content, helping search engines index the page.

         ```html
         <meta name="keywords" content="keyword1, keyword2, keyword3" />
         ```

       - **Open Graph Metadata**: This metadata enhances the way a webpage is represented when shared on social media platforms, providing information such as the title, description, and preview image.

         ```html
         <meta property="og:title" content="Title Here" />
         <meta property="og:description" content="Description Here" />
         <meta property="og:image" content="image_url_here" />
         ```

       - **Favicon Metadata**: This metadata links the favicon (a small icon) to the webpage, displayed in the browser's address bar or tab.

         ```html
         <link rel="icon" href="path/to/favicon.ico" />
         ```

    1. Adding metadata

       Next.js has a Metadata API that can be used to define your application metadata. There are two ways you can add metadata to your application:

       - **Config-based**: Export a [static `metadata` object](https://nextjs.org/docs/app/api-reference/functions/generate-metadata#metadata-object) or a dynamic [`generateMetadata` function](https://nextjs.org/docs/app/api-reference/functions/generate-metadata#generatemetadata-function) in a `layout.js` or `page.js` file.

       - **File-based**: Next.js has a range of special files that are specifically used for metadata purposes:

         - `favicon.ico`, `apple-icon.jpg`, and `icon.jpg`: Utilized for favicons and icons
         - `opengraph-image.jpg` and `twitter-image.jpg`: Employed for social media images
         - `robots.txt`: Provides instructions for search engine crawling
         - `sitemap.xml`: Offers information about the website's structure

       You have the flexibility to use these files for static metadata, or you can generate them programmatically within your project.

       With both these options, Next.js will automatically generate the relevant `<head>` elements for your pages.

    1. Favicon and Open Graph image

       In the `/public` folder, will notice have two images: `favicon.ico` and `opengraph-image.jpg`.

       Move these images to the root of the `/app` folder.

       After doing this, Next.js will automatically identify and use these files as your favicon and OG image. Can verify this by checking the `<head>` element of your application in dev tools.

       > **Good to know:**
       >
       > You can also create dynamic OG images using the [ImageResponse](https://nextjs.org/docs/app/api-reference/functions/image-response) constructor.

    1. Page title and descriptions

       Can also include a [`metadata` object](https://nextjs.org/docs/app/api-reference/functions/generate-metadata#metadata-fields) from any `layout.js` or `page.js` file to add additional page information like title and description. Any metadata in `layout.js` will be inherited by all pages that use it.

       In the root layout, create a new `metadata` object with the following fields (`/app/layout.tsx`):

       ```tsx title="/app/layout.tsx"
       import { Metadata } from "next"

       export const metadata: Metadata = {
         title: "Acme Dashboard",
         description:
           "The official Next.js Course Dashboard, built with App Router.",
         metadataBase: new URL("https://next-learn-dashboard.vercel.sh"),
       }

       export default function RootLayout() {
         // ...
       }
       ```

       Next.js will automatically add the title and metadata to your application.

       But what if you want to add a custom title for a specific page? You can do this by adding a `metadata` object to the page itself. Metadata in nested pages will override the metadata in the parent.

       For example, in the `/dashboard/invoices` page, you can update the page title (`/app/dashboard/invoices/page.tsx`):

       ```tsx title="/app/dashboard/invoices/page.tsx"
       import { Metadata } from "next"

       export const metadata: Metadata = {
         title: "Invoices | Acme Dashboard",
       }
       ```

       This works, but we are repeating the title of the application in every page. If something changes, like the company name, you'd have to update it on every page.

       Instead, you can use the `title.template` field in the metadata object to define a template for your page titles. This template can include the page title, and any other information you want to include.

       In the root layout, update the `metadata` object to include a template (`/app/layout.tsx`):

       ```tsx title="/app/layout.tsx"
       import { Metadata } from "next"

       export const metadata: Metadata = {
         title: {
           template: "%s | Acme Dashboard",
           default: "Acme Dashboard",
         },
         description:
           "The official Next.js Learn Dashboard built with App Router.",
         metadataBase: new URL("https://next-learn-dashboard.vercel.sh"),
       }
       ```

       The `%s` in the template will be replaced with the specific page title.

       Now, in the `/dashboard/invoices` page can add the page title (`/app/dashboard/invoices/page.tsx`):

       ```tsx title="/app/dashboard/invoices/page.tsx"
       export const metadata: Metadata = {
         title: "Invoices",
       }
       ```

       Navigate to the `/dashboard/invoices` page and check the `<head>` element. Should see the page title is now `Invoices | Acme Dashboard`.

    Adding titles to the other pages:

    - `/login` page
    - `/dashboard/` page
    - `/dashboard/customers` page
    - `/dashboard/invoices/create` page
    - `/dashboard/invoices/[id]/edit` page.
