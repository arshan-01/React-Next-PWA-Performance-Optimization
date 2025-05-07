#### React-Next-PWA-Performance-Optimization


```
import React, { Suspense, useEffect, useRef } from 'react';
import dynamic from 'next/dynamic';
import Head from 'next/head';
import { LazyHydrate } from 'react-lazy-hydration';

// Dynamic import for code-splitting
const LazyComponent = React.lazy(() => import('../components/LazyComponent'));
const PWAInfo = dynamic(() => import('../components/PWAInfo'), { ssr: false });

export default function HomePage({ data }) {
  const observerRef = useRef();

  useEffect(() => {
    const observer = new IntersectionObserver(
      ([entry]) => {
        if (entry.isIntersecting) {
          console.log('Load more triggered');
        }
      },
      { threshold: 0.1 }
    );

    if (observerRef.current) {
      observer.observe(observerRef.current);
    }

    return () => {
      if (observerRef.current) observer.unobserve(observerRef.current);
    };
  }, []);

  return (
    <>
      <Head>
        <title>Optimized React Page</title>
      </Head>

      <main>
        <h1>Welcome to Optimized React App</h1>

        {/* Code splitting using React.lazy */}
        <Suspense fallback={<div>Loading...</div>}>
          <LazyComponent />
        </Suspense>

        {/* PWA Component - only client side */}
        <PWAInfo />

        {/* Lazy Hydration */}
        <LazyHydrate whenVisible>
          <footer>
            <p>This footer hydrates only when visible.</p>
          </footer>
        </LazyHydrate>

        {/* Intersection Observer trigger */}
        <div ref={observerRef} style={{ height: '100px', background: '#ddd' }}>
          Scroll here to load more...
        </div>
      </main>
    </>
  );
}

// Example Next.js SSR function
export async function getServerSideProps() {
  const data = await fetch('https://api.example.com/data').then(res => res.json());
  return { props: { data } };
}


```
