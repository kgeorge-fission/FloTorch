<script setup lang="ts">
import { pascalCase } from "scule";
const route: RouteLocationNormalizedLoaded = useRoute();

const props = withDefaults(
  defineProps<{
    showHomeIcon: boolean;
    customLabels: CustomLabels;
  }>(),
  {
    showHomeIcon: true,
    customLabels: () => ({}),
  }
);

const spacingLabels:any = {
  "validexperiments" : "Valid Experiments"
}

// Function to capitalize and format path segments
const formatPathSegment = (segment: string): string => {
  // Check if there's a custom label first
  if (props.customLabels[segment]) {
    return props.customLabels[segment];
  }else if(spacingLabels[segment]){
    return spacingLabels[segment]
  }else{
// Remove hyphens and underscores, then capitalize
  return pascalCase(segment);
  }

  
};

// Computed property to generate breadcrumb items
const breadcrumbItems = computed((): BreadcrumbItem[] => {
  const pathSegments: string[] = route.path
    .split("/")
    .filter((segment: string) => segment);
  let currentPath = "";

  // Start with home
  const items: BreadcrumbItem[] = [
    {
      label: "Home",
      to: "/",
    },
  ];

  // Build up the breadcrumb items
  pathSegments.forEach((segment: string, index: number) => {
    currentPath += `/${segment}`;

    // Check if this is a dynamic route parameter
    const isDynamicSegment: string | undefined = route.params[segment] as
      | string
      | undefined;
     
    items.push({
      label: isDynamicSegment
        ? String(route.params[segment])
        : formatPathSegment(segment),
      to: currentPath,
      // Only make it clickable if it's not the current page
      disabled: index === pathSegments.length - 1,
    });
  });

  return items;
});

const handleRedirect = (e: any) => {
 if(e.target.innerText === 'Projects'){
  window.location.href = '/projects';
 }
}
</script>

<!-- components/DynamicBreadcrumb.vue -->
<template>
  <UBreadcrumb
    v-if="breadcrumbItems.length > 0"
    :items="breadcrumbItems"
    class="py-4 secondary-breadcrumb ml-1"
    @click="(e) => handleRedirect(e)"
  />
</template>