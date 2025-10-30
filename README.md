# Svelte/SvelteKit for Convox

A production-ready SvelteKit application configured for deployment on Convox.

[SvelteKit](https://svelte.dev/docs/kit) is the official application framework for Svelte, providing a full-stack development experience with server-side rendering, routing, and build optimizations. This example demonstrates how to deploy a SvelteKit app on Convox with automatic SSL, load balancing, and zero-downtime deployments.

Deploy to Convox Cloud for a fully-managed platform experience, or to your own Convox Rack for complete control over your infrastructure. Either way, you'll get automatic SSL, load balancing, and zero-downtime deployments out of the box.

## Features

- **Modern Web Framework** - Built with SvelteKit for optimal performance and developer experience
- **Server-Side Rendering** - Full SSR support with SvelteKit's adapter-node
- **Interactive Components** - Includes demo components showcasing Svelte's reactive capabilities
- **Container-Ready** - Optimized Docker configuration for production deployments
- **Production Configured** - Environment-based configuration with secure defaults
- **Health Monitoring** - Built-in health check endpoint for reliability
- **Auto-Scaling Ready** - Configured for horizontal scaling based on load
- **Tailwind CSS** - Pre-configured with Tailwind for rapid UI development

## Deploy to Convox Cloud

1. **Create a Cloud Machine** at [console.convox.com](https://console.convox.com)

2. **Create the app**:
```bash
convox cloud apps create svelte-app -i your-machine-name
```

3. **Deploy the app**:
```bash
convox cloud deploy -a svelte-app -i your-machine-name
```

4. **View your app**:
```bash
convox cloud services -a svelte-app -i your-machine-name
```

Visit your URL to see the SvelteKit app running!

## Deploy to Convox Rack

1. **Create the app**:
```bash
convox apps create svelte-app
```

2. **Deploy the app**:
```bash
convox deploy -a svelte-app
```

3. **View your app**:
```bash
convox services -a svelte-app
```

Visit your URL to see the SvelteKit app running!

## Application Features

The example SvelteKit application includes:

- **Home Page** - Welcome page with interactive counter component
- **About Page** - Static page demonstrating SSR capabilities
- **Sverdle Game** - A Wordle clone showcasing form handling and server-side logic
- **Reactive Components** - Demonstrates Svelte's reactive features and animations

### Testing the Application

```bash
# Get the service URL
convox services -a svelte-app

# Test the application
curl https://web.svelte-app.cloud.convox.com/

# Test the about page
curl https://web.svelte-app.cloud.convox.com/about

# Access the Sverdle game
curl https://web.svelte-app.cloud.convox.com/sverdle
```

## Configuration

### Environment Variables

Configure your application using environment variables:

```bash
convox env set \
  NODE_ENV=production \
  PUBLIC_API_URL=https://api.example.com \
  -a svelte-app
```

### Scaling

Adjust the number of running processes and resources:

```bash
# Scale to 3 instances with specific resources
convox scale web --count 3 --cpu 512 --memory 1024 -a svelte-app
```

Or configure autoscaling in your `convox.yml`:

```yaml
services:
  web:
    build: .
    port: 3000
    scale:
      count: 1-5
      cpu: 256
      memory: 512
      targets:
        cpu: 70
        memory: 80
```

## Development

### Local Development

1. **Install dependencies**:
```bash
npm install
```

2. **Run development server**:
```bash
npm run dev
# or start with auto-open
npm run dev -- --open
```

The app will be available at `http://localhost:5173`

3. **Build for production**:
```bash
npm run build
```

4. **Preview production build**:
```bash
npm run preview
```

### Local Development with Convox

Use Convox to run the application locally with Docker:

```bash
convox start
```

This will build and run the application in a local Docker environment that mimics production.

## Project Structure

```
.
├── src/
│   ├── app.html              # HTML template
│   ├── app.css               # Global styles with Tailwind
│   ├── routes/               # SvelteKit routes
│   │   ├── +page.svelte      # Home page
│   │   ├── +layout.svelte    # Root layout
│   │   ├── about/            # About page
│   │   └── sverdle/          # Sverdle game
│   └── lib/                  # Shared components and utilities
├── static/                   # Static assets
├── Dockerfile               # Docker configuration
├── convox.yml              # Convox deployment configuration
├── package.json            # Node.js dependencies
├── svelte.config.js        # SvelteKit configuration
└── vite.config.ts          # Vite bundler configuration
```

## Docker Configuration

The application uses a simple, efficient Docker configuration:

1. **Base Image**: Node.js 22 Alpine for minimal size
2. **Build Stage**: Compiles the SvelteKit application
3. **Runtime**: Runs the production build with Node.js

This results in small, secure production images optimized for fast deployments.

## Monitoring

### View Logs

Cloud:
```bash
convox cloud logs -a svelte-app -i your-machine-name
```

Rack:
```bash
convox logs -a svelte-app
```

### Health Check

The application includes a built-in health check endpoint:

```bash
curl https://your-svelte-app-url/
```

## Best Practices

1. **Use Environment Variables**: Never hardcode secrets or configuration
2. **Enable HTTPS**: Always use HTTPS in production (Convox handles this automatically)
3. **Set Resource Limits**: Configure appropriate CPU and memory limits
4. **Use Health Checks**: Ensure your application can be monitored
5. **Optimize Builds**: Keep Docker images small with production dependencies only
6. **Regular Updates**: Keep SvelteKit and dependencies updated

## Troubleshooting

### Application Won't Start

Check that the application is binding to the correct port:
```bash
convox logs -a svelte-app --filter "listening"
```

Verify environment variables:
```bash
convox env -a svelte-app
```

### Build Failures

If the build fails, check the build logs:
```bash
convox builds logs BUILD_ID -a svelte-app
```

Common issues:
- Ensure all required dependencies are in package.json
- Check that the Dockerfile references the correct Node.js version
- Verify the build script in package.json is correct

### Performance Issues

Monitor resource usage:
```bash
convox ps -a svelte-app
```

Consider scaling up if CPU or memory usage is high:
```bash
convox scale web --cpu 1000 --memory 2048 -a svelte-app
```

## Advanced Configuration

### Custom Domains

Add your custom domain:
```bash
convox services -a svelte-app
# Note the router domain, then create a CNAME to it
```

### Database Integration

To add a PostgreSQL database:

1. Update your `convox.yml`:
```yaml
resources:
  database:
    type: postgres
    options:
      storage: 10
      version: 13

services:
  web:
    build: .
    port: 3000
    resources:
      - database
```

2. Access the database URL in your application:
```javascript
// In your SvelteKit app
const databaseUrl = process.env.DATABASE_URL;
```

### Adding Background Jobs

Add a worker service to your `convox.yml`:
```yaml
services:
  web:
    build: .
    port: 3000
  worker:
    build: .
    command: node worker.js
    scale:
      count: 1
      cpu: 256
      memory: 512
```

### Static Asset Optimization

For serving static assets efficiently, you can configure caching headers and CDN integration:

```yaml
services:
  web:
    build: .
    port: 3000
    environment:
      - PUBLIC_CDN_URL=${CDN_URL}
```

## SvelteKit-Specific Considerations

### Adapter Configuration

This example uses `@sveltejs/adapter-node` for Node.js deployments. The configuration in `svelte.config.js` is optimized for containerized environments.

### Environment Variables

SvelteKit distinguishes between private and public environment variables:
- Private variables (like API keys) are only available server-side
- Public variables (prefixed with `PUBLIC_`) are available in both server and client code

### Build Output

The production build creates:
- Server-side code in the `build` directory
- Client-side assets optimized for performance
- Automatic code-splitting for optimal loading

## Resources

- [SvelteKit Documentation](https://svelte.dev/docs/kit)
- [Svelte Documentation](https://svelte.dev/docs)
- [Svelte Tutorial](https://learn.svelte.dev)
- [Convox Documentation](https://docs.convox.com)
- [Tailwind CSS Documentation](https://tailwindcss.com/docs)

## Support

- Svelte Community: [Discord](https://svelte.dev/chat) | [Reddit](https://www.reddit.com/r/sveltejs/)
- Convox Support: cloud-support@convox.com
- GitHub Issues: [Create an issue](https://github.com/convox-examples/svelte/issues)