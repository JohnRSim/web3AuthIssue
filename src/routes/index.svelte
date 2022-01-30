<script context="module">
  /**
   * @type {import('@sveltejs/kit').Load}
   */
  export async function load({ fetch }) {
    const url = './index.json';
    const response = await fetch(url);

    if (response.ok) {
      return {
        props: { ...(await response.json()) },
      };
    }

    return {};
  }
</script>

<script>
  import { browser } from '$app/env';
	import { onMount } from 'svelte';

  import ogSquareImageSrc from '$lib/assets/home/home-open-graph-square.jpg';
  import ogImageSrc from '$lib/assets/home/home-open-graph.jpg';
  import twitterImageSrc from '$lib/assets/home/home-twitter.jpg';
  import featuredImageSrc from '$lib/assets/home/home.jpg';
  import BlogRoll from '$lib/components/BlogRoll.svelte';
  import Card from '$lib/components/Card.svelte';
  import SEO from '$lib/components/SEO/index.svelte';
  import website from '$lib/config/website';

  //
  //import { CHAIN_NAMESPACES, CustomChainConfig } from "@web3auth/base";
  
	import { svelteWeb3 } from '@chiuzon/svelteweb3'
  const { error, account } = svelteWeb3()

  export let posts;

  let web3auth;
  let ADAPTER_EVENTS_GLOB;
  onMount(async () => { 
    //if (browser) {
      const { Buffer } = await import('buffer') 
      window.Buffer = Buffer;
    //}
    const { Web3Auth } = await import("@web3auth/web3auth") 
    const { ADAPTER_EVENTS, CHAIN_NAMESPACES, CustomChainConfig } = await import("@web3auth/base") 
    const {  LOGIN_MODAL_EVENTS  } = await import("@web3auth/ui") 
    ADAPTER_EVENTS_GLOB = ADAPTER_EVENTS;
    const polygonMumbaiConfig = {
      ...CustomChainConfig,
      chainNamespace: CHAIN_NAMESPACES.EIP155,
      rpcTarget: "https://rpc-mumbai.maticvigil.com",
      blockExplorer: "https://mumbai-explorer.matic.today",
      chainId: "0x13881",
      displayName: "Polygon Mumbai Testnet",
      ticker: "matic",
      tickerName: "matic",
    };

    //const provider = await web3auth.connect();

    web3auth = new Web3Auth({
        chainConfig: polygonMumbaiConfig,
        clientId: 'BLpa7Xu1dSLQcdWaot0sH6IlyYyviJdLP-3k1iO4hyt95rOwpTFkqfmB-FLP7lqiTcI0ibN8cDpZudsSqCJE6Ls' // get your clientId from https://developer.web3auth.io
    });

    subscribeAuthEvents(web3auth);
    console.log(ADAPTER_EVENTS_GLOB);
    console.log(web3auth.initModal);
    await web3auth.initModal();
  });

  function subscribeAuthEvents(web3auth) {
    //console.log('xx',ADAPTER_EVENTS_GLOB);
    

      web3auth.on(ADAPTER_EVENTS_GLOB.CONNECTED, (data) => {
          console.log("Yeah!, you are successfully logged in", data);

        });
        web3auth.on(ADAPTER_EVENTS_GLOB.CONNECTING, () => {
          console.log("connecting");
        });

        web3auth.on(ADAPTER_EVENTS_GLOB.DISCONNECTED, () => {
          console.log("disconnected");
        });

        web3auth.on(ADAPTER_EVENTS_GLOB.ERRORED, (error) => {
          console.log("some error or user have cancelled login request", error);
        });

        web3auth.on(ADAPTER_EVENTS_GLOB.MODAL_VISIBILITY, (isVisible) => {
          console.log("modal visibility", isVisible);
        });
  }



















  const { author, siteUrl } = website;

  let title = 'Home';
  const breadcrumbs = [
    {
      name: 'Home',
      slug: '',
    },
  ];
  let metadescription =
    'SvelteKit MDsvex Blog Starter - starter code by Rodney Lab to help you get going on your next blog site';
  const featuredImageAlt =
    'picture of a person with long, curly hair, wearing a red had taking a picture with an analogue camera';
  const featuredImage = {
    url: featuredImageSrc,
    alt: featuredImageAlt,
    width: 672,
    height: 448,
    caption: 'Home page',
  };
  const ogImage = {
    url: ogImageSrc,
    alt: featuredImageAlt,
  };
  const ogSquareImage = {
    url: ogSquareImageSrc,
    alt: featuredImageAlt,
  };

  const twitterImage = {
    url: twitterImageSrc,
    alt: featuredImageAlt,
  };
  const entityMeta = {
    url: `${siteUrl}/`,
    faviconWidth: 512,
    faviconHeight: 512,
    caption: author,
  };
  const seoProps = {
    title,
    slug: '',
    entityMeta,
    datePublished: '2021-07-07T14:19:33.000+0100',
    lastUpdated: '2021-07-07T14:19:33.000+0100',
    breadcrumbs,
    metadescription,
    featuredImage,
    ogImage,
    ogSquareImage,
    twitterImage,
  };
</script>

<SEO {...seoProps} />
<header>
  <h1>Climate &mdash; Sveltekit Starter</h1>
  <h2>SvelteKit MDsveX (Markdown for Svelte) Blog</h2>
</header>
<Card>
  <h2><span>About me</span></h2>
  <p>
    I live and breathe analogue photography. I show you my favourite analogue film cameras on this
    site. Hopefully if you are not into analogue photography yet, some of my enthusiasm will rub off
    onto you.
  </p>
</Card>
<BlogRoll {posts} />

<style lang="scss">
  header > h2 {
    font-size: $font-size-3;
  }
</style>
