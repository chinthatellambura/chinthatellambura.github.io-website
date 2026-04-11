---
title: "Publications by Time"
layout: single
permalink: /publications/time/
author_profile: true
toc: false
---

<div class="notice--info">
<strong>Copyright notice:</strong>
Materials presented here are to ensure timely dissemination of scholarly and technical work.
Copyright and all rights therein are retained by authors or by other copyright holders.
All persons copying this information are expected to adhere to the terms and constraints invoked by each author's copyright.
In most cases, these works may not be reposted without the explicit permission of the copyright holder.
</div>

<p id="ieeeNotice">
  <strong>IEEE Copyright Notice:</strong>
  &copy; <span id="copyrightYear"></span> IEEE. Personal use of this material is permitted.
  Permission from IEEE must be obtained for all other uses, in any current or future media,
  including reprinting/republishing this material for advertising or promotional purposes,
  creating new collective works, for resale or redistribution to servers or lists, or reuse
  of any copyrighted component of this work in other works.
</p>

<p>Please click a year to view the corresponding publications.</p>

<div id="year-links" style="margin-bottom: 1.5rem;"></div>

<h2 id="year-heading">Publications</h2>

<ol id="pubList"></ol>

<script>
(function () {
  const currentYear = new Date().getFullYear();
  const minYear = 1991;

  const params = new URLSearchParams(window.location.search);
  const requestedYear = parseInt(params.get("year"), 10);

  let year;
  if (isNaN(requestedYear)) {
    year = currentYear;
  } else if (requestedYear > currentYear) {
    year = currentYear;
  } else if (requestedYear < minYear) {
    year = minYear;
  } else {
    year = requestedYear;
  }

  document.getElementById("year-heading").textContent = "Publications in " + year;
  document.getElementById("copyrightYear").textContent = year;

  function buildYearLinks() {
    const container = document.getElementById("year-links");
    const years = [];

    for (let y = currentYear; y >= minYear; y--) {
      years.push(y);
    }

    container.innerHTML = years.map(y => {
      if (y === year) return `<strong>${y}</strong>`;
      return `<a href="?year=${y}">${y}</a>`;
    }).join(" | ");
  }

  async function loadYearFile() {
    const fileName = "{{ site.baseurl }}/assets/publications/" + year + ".html";
    const pubList = document.getElementById("pubList");

    try {
      const response = await fetch(fileName);
      if (!response.ok) throw new Error("Could not load file");

      const html = await response.text();
      const parser = new DOMParser();
      const doc = parser.parseFromString(html, "text/html");
      const items = doc.getElementsByTagName("li");

      pubList.innerHTML = "";

      for (let i = 0; i < items.length; i++) {
        pubList.appendChild(items[i].cloneNode(true));
      }

      if (pubList.children.length === 0) {
        pubList.innerHTML = "<li>No publications found for this year.</li>";
      }
    } catch (e) {
      pubList.innerHTML = `<li>Unable to load publication file for ${year}.</li>`;
    }
  }

  buildYearLinks();
  loadYearFile();
})();
</script>
