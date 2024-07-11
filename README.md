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

   Use <Link> component to do `client-side navigation` with JavaScript insteed `<a>` HTML hyperlink tag:

   1. Replace the <a> tag with <Link> into `/app/ui/dashboard/nav-links.tsx` file:

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
