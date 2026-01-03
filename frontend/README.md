Frontend Application – React Setup Guide
Overview

This frontend application is built using React and serves as the user interface for the Multi-Tenant SaaS Platform.
It provides a responsive dashboard for authentication, project management, task tracking, and user interactions.

The application runs in both development and production modes using standard npm scripts.

Available Scripts

All commands should be executed from the frontend project directory.

▶️ Start Development Server
npm start 


Runs the application in development mode

Opens automatically at: http://localhost:3000

Supports hot reload on file changes

Displays lint and runtime errors in the console

🧪 Run Tests
npm test


Launches the test runner in interactive watch mode

Used to validate UI components and logic during development

🏗 Build for Production
npm run build


Creates an optimized production build in the build/ folder

Bundles and minifies assets for best performance

Generates hashed filenames for efficient caching

Output is ready for deployment to a web server or container

⚠️ Eject Configuration (Advanced)
npm run eject


Warning: This is a one-way operation and cannot be undone.

Exposes full build configuration (Webpack, Babel, ESLint)

Use only if deep customization is required

Not recommended for standard academic or production use

Development Notes

The development server automatically reloads when source files change

Console warnings and errors help identify issues early

The default configuration is sufficient for most use cases

Deployment Notes

Always run npm run build before deploying

Serve the contents of the build/ directory using:

Nginx

Apache

Docker container

Cloud hosting platforms

Additional Resources

React Documentation: https://reactjs.org

React Scripts & Tooling: https://facebook.github.io/create-react-app/docs/getting-started