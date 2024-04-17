<script>
	import '@evidence-dev/tailwind/fonts.css';
	import '../app.css';
	import { EvidenceDefaultLayout } from '@evidence-dev/core-components';
	export let data;
</script>

<EvidenceDefaultLayout {data} logo={'/long_logo_1_BW_cropped_transparent.png'} neverShowQueries={true} hideSidebar={true}>
	<slot slot="content" />
</EvidenceDefaultLayout>