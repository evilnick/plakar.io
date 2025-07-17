---
date: "2025-07-15T19:15:41Z"
title: services
summary: "Manage optional Plakar-connected services"
---
<div class="head" role="doc-pageheader" aria-label="Manual header
  line"><span class="head-ltitle">PLAKAR-SERVICES(1)</span>
  <span class="head-vol">General Commands Manual</span>
  <span class="head-rtitle">PLAKAR-SERVICES(1)</span></div>
<main class="manual-text">
<section class="Sh">
<h2 class="Sh" id="NAME"><a class="permalink" href="#NAME">NAME</a></h2>
<p class="Pp"><code class="Nm">plakar-services</code> &#x2014;
    <span class="Nd" role="doc-subtitle">Manage optional Plakar-connected
    services</span></p>
</section>
<section class="Sh">
<h2 class="Sh" id="SYNOPSIS"><a class="permalink" href="#SYNOPSIS">SYNOPSIS</a></h2>
<table class="Nm">
  <tr>
    <td><code class="Nm">plakar services status
      <var class="Ar">service_name</var></code></td>
    <td></td>
  </tr>
</table>
<br/>
<table class="Nm">
  <tr>
    <td><code class="Nm">plakar services enable
      <var class="Ar">service_name</var></code></td>
    <td></td>
  </tr>
</table>
<br/>
<table class="Nm">
  <tr>
    <td><code class="Nm">plakar services disable
      <var class="Ar">service_name</var></code></td>
    <td></td>
  </tr>
</table>
</section>
<section class="Sh">
<h2 class="Sh" id="DESCRIPTION"><a class="permalink" href="#DESCRIPTION">DESCRIPTION</a></h2>
<p class="Pp">The <code class="Nm">plakar services</code> command allows you to
    enable, disable, and inspect additional services that integrate with the
    <code class="Nm">plakar</code> platform via
    <a class="Xr" href="../plakar-login/" aria-label="plakar-login, section
    1">plakar-login(1)</a> authentication. These services connect to the
    plakar.io infrastructure, and should only be enabled if you agree to
    transmit non-sensitive operational data to plakar.io.</p>
<p class="Pp">All subcommands require prior authentication via
    <a class="Xr" href="../plakar-login/" aria-label="plakar-login, section
    1">plakar-login(1)</a>.</p>
<p class="Pp">At present, only the &#x201C;alerting&#x201D; service is
    available. When enabled, alerting will:</p>
<ol class="Bl-enum">
  <li>Send email notifications when operations fail.</li>
  <li>Expose the latest alerting reports in the Plakar UI (see
      <a class="Xr" href="../plakar-ui/" aria-label="plakar-ui, section
      1">plakar-ui(1)</a>).</li>
</ol>
<p class="Pp">By default, all services are disabled.</p>
</section>
<section class="Sh">
<h2 class="Sh" id="SUBCOMMANDS"><a class="permalink" href="#SUBCOMMANDS">SUBCOMMANDS</a></h2>
<dl class="Bl-tag">
  <dt><var class="Ar">status</var> <var class="Ar">service_name</var></dt>
  <dd>Display the current configuration status (enabled or disabled) of the
      named service. Currently, only the &quot;alerting&quot; service is
      supported.</dd>
  <dt><var class="Ar">enable</var> <var class="Ar">service_name</var></dt>
  <dd>Enable the specified service. Currently, only the &quot;alerting&quot;
      service is supported.</dd>
  <dt><var class="Ar">disable</var> <var class="Ar">service_name</var></dt>
  <dd>Disable the specified service. Currently, only the &quot;alerting&quot;
      service is supported.</dd>
</dl>
</section>
<section class="Sh">
<h2 class="Sh" id="EXAMPLES"><a class="permalink" href="#EXAMPLES">EXAMPLES</a></h2>
<p class="Pp">Check the status of the alerting service:</p>
<div class="Bd Pp Bd-indent Li">
<pre>$ plakar services status alerting</pre>
</div>
<p class="Pp">Enable alerting:</p>
<div class="Bd Pp Bd-indent Li">
<pre>$ plakar services enable alerting</pre>
</div>
<p class="Pp">Disable alerting:</p>
<div class="Bd Pp Bd-indent Li">
<pre>$ plakar services disable alerting</pre>
</div>
</section>
<section class="Sh">
<h2 class="Sh" id="SEE_ALSO"><a class="permalink" href="#SEE_ALSO">SEE
  ALSO</a></h2>
<p class="Pp"><a class="Xr" href="../plakar-login/" aria-label="plakar-login,
    section 1">plakar-login(1)</a>,
    <a class="Xr" href="../plakar-ui/" aria-label="plakar-ui, section
    1">plakar-ui(1)</a></p>
</section>
</main>
<div class="foot" role="doc-pagefooter" aria-label="Manual footer
  line"><span class="foot-left">Plakar</span> <span class="foot-date">July 8,
  2025</span> <span class="foot-right">PLAKAR-SERVICES(1)</span></div>
