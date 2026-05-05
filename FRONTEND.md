# Coffee Store E-Commerce Application
 This project is a responsive web application designed to showcase and manage coffee-related products, featuring a product listing page with filters, a product card display, and an accessible user interface.

## Project Overview
This application is built to provide an intuitive shopping experience for coffee enthusiasts, allowing users to browse products, apply filters, and view details in a responsive layout. The project leverages modern web technologies to ensure compatibility across devices and accessibility for all users.

Developed By: [siddami](https://github.com/siddami)

## Features
Responsive Product Listing: Displays products in a grid layout with 1 column on small screens, 2 columns on medium screens, and up to 3 columns on large screens.
1. Filter Sidebar: Includes a toggleable sidebar on small screens and a static sidebar on larger screens for filtering by search, category, and dietary options.
2. Product Card Display: Shows product details (name, brand, price) with a favorite toggle, optimized for responsiveness and accessibility.
3. Top Filters: Allows sorting by "Bestselling" and toggling image-only views, with a responsive design.
4. Accessibility: Implements ARIA labels, keyboard navigation, and sufficient color contrast for an inclusive experience.
5. Overlay Sidebar: On small screens, the filter sidebar overlays the product list, auto-closing after filter application or manual closure.
Tools and Technologies
6. Loader: displays before data pops up.

Framework: Next.js - For server-side rendering and static site generation.
Language: TypeScript - For type safety and improved development experience.
Styling: Tailwind CSS - For utility-first responsive design.

Image Optimization: I used the Next.js Image Component - For optimized and responsive images.
State Management: React Hooks (useState, useEffect) - For managing component state and side effects.
Build Tool: npm - For package management and building the project.
File System: Node.js fs module - Used for file-based data handling where fetch was not viable.

## Usage
 - Access the application at [https://my-coffee-app-beta.vercel.app/] during development.
 - Use the toggle button on small screens to open the filter sidebar, which overlays the product list.
 - Apply filters (search, category, dietary) to refine the product list, which will auto-close the sidebar.
 - Click the "Close" button in the sidebar to manually close it.
 - Browse products, toggle favorites, and sort using the top filters.

## Challenges Faced During Development
During the development process, several challenges were encountered and resolved:

 1. Fetch Issue with Data Loading: Initially, the fetch API was used to load product data, but it caused runtime errors due to server-side rendering constraints. To resolve this, the fs module was integrated to read product data from local files, ensuring consistent data availability during builds and runtime.

 2. TypeScript Module Resolution: Encountered errors with missing type declarations for custom modules (e.g., Product, ProductCard). This was fixed by ensuring all TypeScript files had proper import paths and type definitions.

 3. Sidebar Toggle Behavior: The sidebar toggle initially stacked elements or failed to overlay the product list. This was addressed by restructuring the layout to use a fixed overlay with proper z-index and transition effects.
 
 4. ESLint Build Errors: Unused variables (setProducts, dietArray, Product) caused build failures. These were resolved by removing unused state setters and imports, aligning with ESLint rules.

 3. Responsiveness Across Breakpoints: Ensuring the grid and sidebar adapted smoothly across all screen sizes required iterative adjustments to Tailwind CSS breakpoints and gap spacing.

## Future Improvements
1. Add dynamic product data fetching with API integration.
2. Implement a backend to persist favorite selections.
3. Enhance filter options with more sorting criteria.
4. Optimize performance for large product catalogs.

## Screenshot
![screenshot](/public/images/coffee.png)