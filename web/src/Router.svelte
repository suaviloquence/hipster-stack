<script lang="ts">
  import type { SvelteComponent } from "svelte";
	import One from "./pages/One.svelte";
	import Two from "./pages/Two.svelte";

  /// e.g., set to /app so /home corresponds to example.com/app/home
  const PREFIX = "";

  type Component = typeof SvelteComponent;

  const routes: Record<string, Component> = {
		"/one": One,
		"/two/(?<name>\\w+)": Two,
  };

  const compiled: [RegExp, Component][] = Object.entries(routes).map(
    ([route, component]) => [new RegExp("^" + PREFIX + route + "$"), component]
  );

  export let defaultComponent: Component;

  let currentComponent = defaultComponent;
  let props: Record<string, any> = {};

  path.subscribe((path) => {
    history.pushState({}, "", path);
    updateRoute(path);
  });

  window.onpopstate = () => {
    updateRoute(window.location.pathname);
  };

  function updateRoute(path: string) {
    for (const [route, component] of compiled) {
      let match = path.match(route);
      if (match) {
        props = match.groups;
        currentComponent = component;
        return;
      }
    }
    props = {};
    currentComponent = defaultComponent;
  }

  updateRoute(window.location.pathname);
</script>

<svelte:component this={currentComponent} {...props} />
