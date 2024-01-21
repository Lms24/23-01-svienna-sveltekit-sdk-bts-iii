<script lang="ts">
	import Slide from './slide.svelte'
	import Markdown from './markdown.svelte'
	import Code from './code.svelte'
	import Slides from './slides.svelte'
</script>

<Slide>
	<div class="flex flex-row w-full justify-between">
		<div class="flex flex-col">
			<h3>Hi 👋</h3>

			<p>
				I'm Lukas, I work at <a href="https://sentry.io/welcome">Sentry</a>
			</p>

			<p>Sentry JavaScript SDKs</p>

			<p>SvelteKit SDK</p>
		</div>
		<div class="flex flex-col items-center">
			<img src="/ls-2.jpg" class="rounded-full w-64" alt="Lukas Stracke" />
			<p class="text-[1.5rem]">Lukas Stracke</p>
			<ul class="text-sm list-none">
				<li>GitHub: <a href="https://github.com/Lms24">@Lms24</a></li>
				<li>Twitter: <a href="https://twitter.com/lukasstracke">@lukasstracke</a></li>
			</ul>
		</div>
	</div>
</Slide>

<Slide>
	<h3>One Year Ago...</h3>
	<img src="/gh-kit-sdk-roadmap.png" alt="github issue" class="border-gray-500 border-[1px]" />
</Slide>

<Slide hidden>
	<h3>Today</h3>
	<iframe
		class="w-full h-[60vh]"
		title="NPM Trends"
		src="https://npmtrends.com/@sentry/sveltekit"
	/>
</Slide>

<Slide>
	<h3><code>@sentry/sveltekit</code></h3>
	<img src="/sentry-svelte.jpeg" alt="github issue" class="border-gray-500 border-[1px]" />
	<p>Behind the Scenes</p>
	<p class="text-sm">Building an Error and Performance Monitoring SDK for SvelteKit</p>
</Slide>

<Markdown name="intro.md" external />

<Slide>
	<h3>"Monkey Patching"</h3>

	<Code id="code" lines="1|3-6">
		{`
			const original = window.onerror;

			window.onerror = (...args) => {
				Sentry.captureException(args);
				return original(args);
			}
		`}
	</Code>

	<div class="fragment flex flex-col text-md items-center">
		<p>Alternatives</p>
		<ul>
			<li><code>Proxy</code> API</li>
			<li>Node: <code>diagnostics_channel</code> API</li>
			<li>Node: module loaders</li>
		</ul>
	</div>
</Slide>

<Slide>
	<h3>SDK Tasks</h3>

	<ul>
		<li>Instrument global APIs (e.g. <code>window.onerror</code>)</li>
		<li class="fragment">Capture errors, spans (+more)</li>
		<li class="fragment">Hold contextual, scoped data (e.g. <code>setUser()</code>)</li>
		<li class="fragment">Convert to <code>Event</code>, apply and process data</li>
		<li class="fragment">Send events to Sentry</li>
	</ul>
</Slide>

<Slide hidden>
	<img src="/nx-graph.png" alt="SDK Monorepo tree" class="border-gray-500 border-[1px]" />
	<a href="http://127.0.0.1:4211/projects/%40sentry%2Fsveltekit?searchDepth=2">Nx Monorepo Graph</a>
</Slide>

<Slide>
	<img src="/kit-sdk-architecture.png" alt="github issue" class="border-gray-500 border-[1px]" />
	<div
		class="fragment fade-in-then-out absolute top-80 bottom-6 left-28 right-40 border-red-600 border-2"
	/>
	<div
		class="fragment fade-in-then-out absolute top-6 bottom-80 left-1 right-14 border-red-600 border-2"
	/>
	<div
		class="fragment fade-in-then-out absolute top-6 bottom-6 left-1 w-80 border-red-600 border-2"
	/>
	<div
		class="fragment fade-in-then-out absolute top-6 bottom-6 right-14 w-72 border-red-600 border-2"
	/>
	<div
		class="fragment fade-in-then-out absolute top-36 bottom-32 left-80 w-72 border-red-600 border-2"
	/>
</Slide>

<Markdown name="challenges-1.md" external />

<Slide>
	Initial HTML Response:
	<img
		src="/initial-html.png"
		alt="initial HTML response from sveltekit"
		class="border-gray-500 border-[1px]"
	/>
	<div class="absolute bottom-[7rem] left-48 right-14 h-[11rem] border-red-600 border-2" />
</Slide>

<Markdown name="challenges-2.md" external />

<Slide>
	<div class="flex flex-row w-full gap-8">
		<div class="flex flex-col">
			<h3>Proper Solution</h3>
			<ul>
				<li>We can't solve this on our end!</li>
				<li>
					So let's <a href="https://github.com/sveltejs/kit/pull/10009">contribute</a> to SvelteKit!
				</li>
				<li>
					Simple fix: Use current <code>window.fetch</code> in <code>load</code> fetch
				</li>
			</ul>
		</div>
		<img src="/fetch-pr.png" alt="Fetch fix PR to Sveltekit" class="w-1/3 aspect-auto h-1/5" />
	</div>
</Slide>

<Slide>
	<Code lines="[]"
		>{`
	// start.js	
	export const load_fetch = (url, opts) => {
		// SvelteKit's cache logic
		return native_fetch(url, opts);
	}
	
	`}</Code
	>
</Slide>

<Slide>
	<Code lines="[]"
		>{`
	// start.js	
	export const load_fetch = (url, opts) => {
		// SvelteKit's cache logic
		return window.fetch(url, opts);
	}
	
	`}</Code
	>
</Slide>

<Slide>
	<p>
		✅ Fixed in <a href="https://github.com/sveltejs/kit/releases/tag/%40sveltejs%2Fkit%401.26.0"
			>SvelteKit 1.26.0</a
		>
	</p>
	<tiny class="text-sm"
		><a
			href="https://github.com/sveltejs/kit/blob/%40sveltejs/kit%401.26.0/packages/kit/src/runtime/client/fetcher.js"
			>(Actual Code)</a
		></tiny
	>
</Slide>

<Markdown name="challenges-3.md" external />

<Markdown name="outro.md" external />

<Slide>
	<div class="flex flex-col gap-8 items-center">
		<p>Thank you!</p>

		<div class="flex flex-col">
			<span class="text-sm">Slides:</span>
			<img src="/qr.png" alt="QR code to slides" class="w-40 h-40" />
		</div>
	</div>
</Slide>
