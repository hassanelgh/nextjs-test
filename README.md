# Folder structure

- `/app`: Contains all the routes, components, and logic for your application, this is where you'll be mostly working from.
- `/app/lib`: Contains functions used in your application, such as reusable utility functions and data fetching functions.
- `/app/ui`: Contains all the UI components for your application, such as cards, tables, and forms. To save time, we've pre-styled these components for you.
- `/public`: Contains all the static assets for your application, such as images.
- `Config Files`: You'll also notice config files such as next.config.js at the root of your application. Most of these files are created and pre-configured when you start a new project using create-next-app. You will not need to modify them in this course.

# Styles

[for more info about styleing ](https://nextjs.org/docs/app/building-your-application/styling)

- You can import `global.css` in any component in your application, but it's usually good practice to add it to your top-level component. In Next.js, this is the root layout
- for adding a style to elementt you can use `className='your-class1 your-class2 '`

## CSS Modules

- `CSS Modules` allow you to scope CSS to a component by automatically creating unique class names, so you don't have to worry about style collisions as well.

1- create a file nome `xxxx.module.css`
2- import the file in page or component you went to add style to it `import styles from '@/.../../xxxxx.module.css';`
3- style elements by adding : `className={styles.your-class1}`

## Using the clsx library to toggle class names

- There may be cases where you may need to conditionally style an element based on state or some other condition. `clsx is a library that lets you toggle class names easily`

```tsx
import clsx from 'clsx';
export default function InvoiceStatus({ status }: { status: string }) {
  return (
    <span
      className={clsx(
        'inline-flex items-center rounded-full px-2 py-1 text-sm',
        {
          'bg-gray-100 text-gray-500': status === 'pending',
          'bg-green-500 text-white': status === 'paid',
        },
      )}
    >
    // ...
)}
```

# Optimizing Fonts

- Recommended reading

  - https://nextjs.org/docs/app/building-your-application/optimizing/fonts
  - https://developer.mozilla.org/en-US/docs/Learn/CSS/Styling_text/Web_fonts
  - https://vercel.com/blog/how-core-web-vitals-affect-seo

- Next.js automatically optimizes fonts in the application when you use the next/font module. It downloads font files at build time and hosts them with your other static assets. This means when a user visits your application, there are no additional network requests for fonts which would impact performance.

## Adding a primary font

- create file `font.ts` and export fonts

```tsx
import { Inter, Lusitana } from "next/font/google";
export const inter = Inter({ subsets: ["latin"] });
export const lusitana = Lusitana({
  weight: ["400", "700"],
  subsets: ["latin"],
});
```

- go to your component or layout that you went to use the font `import { inter } from "@/app/ui/fonts"` and use it by `inter.className`

```tsx
import "@/app/ui/global.css";
import { inter } from "@/app/ui/fonts";
export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="en">
      <body className={`${inter.className} antialiased`}>{children}</body>
    </html>
  );
}
```

# Optimizing images

- Recommended reading:
  - https://nextjs.org/docs/app/building-your-application/optimizing/images
  - https://developer.mozilla.org/en-US/docs/Learn/Performance/Multimedia
  - https://vercel.com/blog/how-core-web-vitals-affect-seo
- Image Optimization is a large topic in web development that could be considered a specialization in itself. Instead of manually implementing these optimizations, you can use the next/image component to automatically optimize your images.
- The `<Image>` Component is an extension of the HTML `<img>` tag, and comes with automatic image optimization, such as:

  - Preventing layout shift automatically when images are loading.
  - Resizing images to avoid shipping large images to devices with a smaller viewport.
  - Lazy loading images by default (images load as they enter the viewport).
  - Serving images in modern formats, like WebP and AVIF, when the browser supports it.

- how to use it :

```tsx
//....
import Image from "next/image";
export default function Page() {
  return (
    // ...
    <div className="flex items-center justify-center p-6 md:w-3/5 md:px-28 md:py-12">
      {/* Add Hero Images Here */}
      <Image
        src="/hero-desktop.png"
        width={1000}
        height={760}
        className="hidden md:block"
        alt="Screenshots of the dashboard project showing desktop version"
      />
    </div>
    //...
  );
}
```

# Layouts and Pages

## Nested routing

- Next.js uses file-system routing where folders are used to create nested routes. Each folder represents a route segment that maps to a URL segment.
- You can create separate UIs for each route using `layout.tsx` and `page.tsx` files.
- `page.tsx` is a special Next.js file that exports a React component, and `it's required for the route to be accessible`. In your application,
- By having a special name for page files, Next.js allows you to colocate UI components, test files, and other related code with your routes. Only the content inside the page file will be publicly accessible. For example, the /ui and /lib folders are colocated inside the /app folder along with your routes.

- The <Layout /> component receives a children prop. This child can either be a page or another layout

```tsx
import SideNav from "@/app/ui/dashboard/sidenav";

export default function Layout({ children }: { children: React.ReactNode }) {
  return (
    <div className="flex h-screen flex-col md:flex-row md:overflow-hidden">
      <div className="w-full flex-none md:w-64">
        <SideNav />
      </div>
      <div className="flex-grow p-6 md:overflow-y-auto md:p-12">{children}</div>
    </div>
  );
}
```

- One benefit of using layouts in Next.js is that on navigation, `only the page components update while the layout won't re-render. This is called partial rendering`
- `Root layout and is required`. Any UI you add to the root layout will be shared across all pages in your application. You can use the root layout to modify your <html> and <body> tags, and add metadata (you'll learn more about metadata in a later chapter).

# Navigating Between Pages

- If you use the <a> HTML element. for navigation you can see that `There's a full page refresh on each page navigation!` for that you need to use `<Link> component `
- In Next.js, you can use the <Link /> Component to link between pages in your application. <Link> allows you to do client-side navigation with JavaScript.

```tsx
import {
  UserGroupIcon,
  HomeIcon,
  DocumentDuplicateIcon,
} from "@heroicons/react/24/outline";
import Link from "next/link";

// ...

export default function NavLinks() {
  return (
    <>
      {links.map((link) => {
        const LinkIcon = link.icon;
        return (
          <Link
            key={link.name}
            href={link.href}
            className="flex h-[48px] grow items-center justify-center gap-2 rounded-md bg-gray-50 p-3 text-sm font-medium hover:bg-sky-100 hover:text-blue-600 md:flex-none md:justify-start md:p-2 md:px-3"
          >
            <LinkIcon className="w-6" />
            <p className="hidden md:block">{link.name}</p>
          </Link>
        );
      })}
    </>
  );
}
```

## Automatic code-splitting and prefetching

- To improve the navigation experience, Next.js automatically code splits your application by route segments. This is different from a traditional React SPA, where the browser loads all your application code on initial load.

- Splitting code by routes means that pages become isolated. If a certain page throws an error, the rest of the application will still work.

- Furthermore, in production, whenever <Link> components appear in the browser's viewport, Next.js automatically prefetches the code for the linked route in the background. By the time the user clicks the link, the code for the destination page will already be loaded in the background, and this is what makes the page transition near-instant!

- for more detail see : https://nextjs.org/docs/app/building-your-application/routing/linking-and-navigating#how-routing-and-navigation-works

## Pattern: Showing active links

- A common UI pattern is to show an active link to indicate to the user what page they are currently on. To do this, you need to get the user's current path from the URL. Next.js `provides a hook called usePathname() that you can use to check the path and implement this pattern`.
- Since usePathname() is a hook, you'll need to turn nav-links.tsx into a Client Component. Add React's "use client" directive to the top of the file, then import usePathname() from next/navigation:

```tsx
"use client";
// ...
import Link from "next/link";
import { usePathname } from "next/navigation";
// ...
const links = [
  { name: "Home", href: "/dashboard", icon: HomeIcon },
  {
    name: "Invoices",
    href: "/dashboard/invoices",
    icon: DocumentDuplicateIcon,
  },
  { name: "Customers", href: "/dashboard/customers", icon: UserGroupIcon },
];
export default function NavLinks() {
  const pathname = usePathname();

  return (
    <>
      {links.map((link) => {
        const LinkIcon = link.icon;
        return (
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
            <LinkIcon className="w-6" />
            <p className="hidden md:block">{link.name}</p>
          </Link>
        );
      })}
    </>
  );
}
```
