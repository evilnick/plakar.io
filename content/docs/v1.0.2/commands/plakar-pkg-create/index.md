---
date: "2025-07-15T19:15:41Z"
title: pkg-create
summary: "Create Plakar plugins"
---
<div class="head" role="doc-pageheader" aria-label="Manual header
  line"><span class="head-ltitle">PLAKAR-PKG-CREATE(1)</span>
  <span class="head-vol">General Commands Manual</span>
  <span class="head-rtitle">PLAKAR-PKG-CREATE(1)</span></div>
<main class="manual-text">
<section class="Sh">
<h2 class="Sh" id="NAME"><a class="permalink" href="#NAME">NAME</a></h2>
<p class="Pp"><code class="Nm">plakar-pkg-create</code> &#x2014;
    <span class="Nd" role="doc-subtitle">Create Plakar plugins</span></p>
</section>
<section class="Sh">
<h2 class="Sh" id="SYNOPSIS"><a class="permalink" href="#SYNOPSIS">SYNOPSIS</a></h2>
<table class="Nm">
  <tr>
    <td><code class="Nm">plakar pkg build
      <var class="Ar">manifest.yaml</var></code></td>
    <td></td>
  </tr>
</table>
</section>
<section class="Sh">
<h2 class="Sh" id="DESCRIPTION"><a class="permalink" href="#DESCRIPTION">DESCRIPTION</a></h2>
<p class="Pp">The <code class="Nm">plakar pkg create</code> assembles a plugin
    using the provided
    <a class="Xr" href="../plakar-pkg-manifest.yaml/" aria-label="plakar-pkg-manifest.yaml,
    section 5">plakar-pkg-manifest.yaml(5)</a>.</p>
<p class="Pp">All the files needed for the plugin need to be already available,
    i.e. executables must be already be built.</p>
<p class="Pp">All external files must reside in the same directory as the
    <var class="Ar">manifest.yaml</var> or in subdirectories.</p>
</section>
<section class="Sh">
<h2 class="Sh" id="SEE_ALSO"><a class="permalink" href="#SEE_ALSO">SEE
  ALSO</a></h2>
<p class="Pp"><a class="Xr" href="../plakar-pkg/" aria-label="plakar-pkg,
    section 1">plakar-pkg(1)</a>,
    <a class="Xr" href="../plakar-pkg-add/" aria-label="plakar-pkg-add, section
    1">plakar-pkg-add(1)</a>,
    <a class="Xr" href="../plakar-pkg-rm/" aria-label="plakar-pkg-rm, section
    1">plakar-pkg-rm(1)</a>,
    <a class="Xr" href="../plakar-pkg-manifest.yaml/" aria-label="plakar-pkg-manifest.yaml,
    section 5">plakar-pkg-manifest.yaml(5)</a></p>
</section>
</main>
<div class="foot" role="doc-pagefooter" aria-label="Manual footer
  line"><span class="foot-left">Plakar</span> <span class="foot-date">July 11,
  2025</span> <span class="foot-right">PLAKAR-PKG-CREATE(1)</span></div>
