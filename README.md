# Next.js TShirt Ecommerce Website


## Features

- Secure user management and authentication with [Supabase](https://supabase.io/docs/guides/auth)
- Powerful data access & management tooling on top of PostgreSQL with [Supabase](https://supabase.io/docs/guides/database)
- Integration with [Stripe Checkout](https://stripe.com/docs/payments/checkout) and the [Stripe customer portal](https://stripe.com/docs/billing/subscriptions/customer-portal)
- Automatic syncing of pricing plans and subscription statuses via [Stripe webhooks](https://stripe.com/docs/webhooks)

## Demo

- https://tshirt-ecommerce-nextjs.vercel.app/

TBD Screenshot here
<!-- [![Screenshot of demo](./public/demo.png)](https://subscription-payments.vercel.app/) -->

## Architecture

TBD
<!-- ![Architecture diagram](./public/architecture_diagram.png) -->

## Getting Started

TBD Guide


### Install dependencies

Ensure you have [pnpm](https://pnpm.io/installation) installed and run:

```bash
pnpm install
```

Next, use the [Vercel CLI](https://vercel.com/docs/cli) to link your project:

```bash
pnpm dlx vercel login
pnpm dlx vercel link
```

`pnpm dlx` runs a package from the registry, without installing it as a dependency. Alternatively, you can install these packages globally, and drop the `pnpm dlx` part.

If you don't intend to use a local Supabase instance for development and testing, you can use the Vercel CLI to download the development env vars:

```bash
pnpm dlx vercel env pull .env.local
```

Running this command will create a new `.env.local` file in your project folder. For security purposes, you will need to set the `SUPABASE_SERVICE_ROLE_KEY` manually from your [Supabase dashboard](https://app.supabase.io/) (`Settings > API`). If you are not using a local Supabase instance, you should also change the `--local` flag to `--linked' or '--project-id <string>' in the `supabase:generate-types` script in `package.json`.(see -> [https://supabase.com/docs/reference/cli/supabase-gen-types-typescript])

### Local development with Supabase

It's highly recommended to use a local Supabase instance for development and testing. We have provided a set of custom commands for this in `package.json`.

First, you will need to install [Docker](https://www.docker.com/get-started/). You should also copy or rename:

- `.env.local.example` -> `.env.local`
- `.env.example` -> `.env`

Next, run the following command to start a local Supabase instance and run the migrations to set up the database schema:

```bash
pnpm supabase:start
```

The terminal output will provide you with URLs to access the different services within the Supabase stack. The Supabase Studio is where you can make changes to your local database instance.

Copy the value for the `service_role_key` and paste it as the value for the `SUPABASE_SERVICE_ROLE_KEY` in your `.env.local` file.

You can print out these URLs at any time with the following command:

```bash
pnpm supabase:status
```

To link your local Supabase instance to your project, run the following command, navigate to the Supabase project you created above, and enter your database password.

```bash
pnpm supabase:link
```

If you need to reset your database password, head over to [your database settings](https://supabase.com/dashboard/project/_/settings/database) and click "Reset database password", and this time copy it across to a password manager! 😄

🚧 Warning: This links our Local Development instance to the project we are using for `production`. Currently, it only has test records, but once it has customer data, we recommend using [Branching](https://supabase.com/docs/guides/platform/branching) or manually creating a separate `preview` or `staging` environment, to ensure your customer's data is not used locally, and schema changes/migrations can be thoroughly tested before shipping to `production`.

Once you've linked your project, you can pull down any schema changes you made in your remote database with:

```bash
pnpm supabase:pull
```

You can seed your local database with any data you added in your remote database with:

```bash
pnpm supabase:generate-seed
pnpm supabase:reset
```

🚧 Warning: this is seeding data from the `production` database. Currently, this only contains test data, but we recommend using [Branching](https://supabase.com/docs/guides/platform/branching) or manually setting up a `preview` or `staging` environment once this contains real customer data.

You can make changes to the database schema in your local Supabase Studio and run the following command to generate TypeScript types to match your schema:

```bash
pnpm supabase:generate-types
```

You can also automatically generate a migration file with all the changes you've made to your local database schema with the following command:

```bash
pnpm supabase:generate-migration
```

And push those changes to your remote database with:

```bash
pnpm supabase:push
```

Remember to test your changes thoroughly in your `local` and `staging` or `preview` environments before deploying them to `production`!

### Use the Stripe CLI to test webhooks

Use the [Stripe CLI](https://stripe.com/docs/stripe-cli) to [login to your Stripe account](https://stripe.com/docs/stripe-cli#login-account):

```bash
pnpm stripe:login
```

This will print a URL to navigate to in your browser and provide access to your Stripe account.

Next, start local webhook forwarding:

```bash
pnpm stripe:listen
```

Running this Stripe command will print a webhook secret (such as, `whsec_***`) to the console. Set `STRIPE_WEBHOOK_SECRET` to this value in your `.env.local` file. If you haven't already, you should also set `NEXT_PUBLIC_STRIPE_PUBLISHABLE_KEY` and `STRIPE_SECRET_KEY` in your `.env.local` file using the **test mode**(!) keys from your Stripe dashboard.

### Run the Next.js client

In a separate terminal, run the following command to start the development server:

```bash
pnpm dev
```

Note that webhook forwarding and the development server must be running concurrently in two separate terminals for the application to work correctly.

Finally, navigate to [http://localhost:3000](http://localhost:3000) in your browser to see the application rendered.