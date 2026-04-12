---
title: "Publications by Category"
layout: single
permalink: /publications/category/
author_profile: true
toc: false
sidebar:
  nav: "publications-nav"
---

<div class="notice--info">
  <strong>Copyright notice:</strong>
  Materials presented here are to ensure timely dissemination of scholarly and technical work.
  Copyright and all rights therein are retained by authors or by other copyright holders.
  All persons copying this information are expected to adhere to the terms and constraints invoked by each author's copyright.
  In most cases, these works may not be reposted without the explicit permission of the copyright holder.
</div>

<p>
  Please select a category to view the corresponding publications.<br>
  <small>* Only journal publications are categorized presently.</small>
</p>

<div style="margin: 1rem 0 1.5rem 0;">
  <label for="categorySelect"><strong>Category:</strong></label><br>
  <select id="categorySelect" name="category" size="15" style="min-width: 320px; max-width: 100%;"></select>
</div>

<h2 id="selected-category-heading">Publications by Category</h2>

<div id="results"></div>

<script>
(function () {
  const categories = {
    "EST": "Channel and Frequency Estimation",
    "CDMA": "Code Division Multiple Access",
    "CODE": "Coding, Trellis Coded Modulation",
    "COGR": "Cognitive Radio",
    "COOPR": "Cooperative Relay Networks",
    "DIV": "Diversity, Channel Fading, Outage, Symbol Error Rate",
    "MIMO": "MIMO: Multiple-input Multiple-output Systems",
    "STC": "MIMO: Space Time Coding, Modulation",
    "MIMOR": "MIMO Relay Networks",
    "MUMIMO": "Multi-user MIMO",
    "DSTR": "Multivariate Distributions, Probability Distributions",
    "NUM": "Numerical Analysis",
    "OFDM": "OFDM: Orthogonal Frequency Division Multiplexing",
    "PAPR": "OFDM: Peak-to-average Power Ratio (PAPR)",
    "RESA": "Resource Allocation, Optimization",
    "DET": "Signal Detection",
    "NOMA": "Non Orthogonal Multiple Access",
    "MMIMO": "Massive MIMO Systems",
    "STOGEO": "Stochastic Geometry",
    "HETNET": "Heterogeneous Networks",
    "TWRN": "Two-way/Multi-way Relay Networks",
    "ML": "Machine Learning for Wireless Systems",
    "BSN": "Backscatter Networks",
    "CFMIMO": "Cell Free Massive MIMO",
    "EH": "Energy Harvesting",
    "MMWAVE": "Millimeter-Wave Communications"
  };

  const categoryKeys = Object.keys(categories);
  const params = new URLSearchParams(window.location.search);
  let selectedCategory = params.get("category");

  if (!selectedCategory || !categories[selectedCategory]) {
    selectedCategory = categoryKeys[0];
  }

  const select = document.getElementById("categorySelect");
  const heading = document.getElementById("selected-category-heading");
  const results = document.getElementById("results");

  heading.textContent = "Publications on '" + categories[selectedCategory] + "'";

  categoryKeys.forEach(function (key) {
    const option = document.createElement("option");
    option.value = key;
    option.textContent = categories[key];
    if (key === selectedCategory) {
      option.selected = true;
    }
    select.appendChild(option);
  });

  select.addEventListener("change", function () {
    const url = new URL(window.location.href);
    url.searchParams.set("category", this.value);
    window.location.href = url.toString();
  });

  function loadYear(year) {
    const url = "{{ site.baseurl }}/assets/publications/" + year + ".html";
    return fetch(url).then(function (response) {
      if (!response.ok) {
        throw new Error("Could not load " + year + ".html");
      }
      return response.text();
    });
  }

  function parseListItems(html) {
    const parser = new DOMParser();
    const doc = parser.parseFromString(html, "text/html");
    return Array.from(doc.getElementsByTagName("li"));
  }

  function itemMatchesCategory(item, categoryCode) {
    const title = item.getAttribute("title");
    if (!title) return false;

    const keys = title.split(",").map(function (s) {
      return s.trim();
    });

    return keys.indexOf(categoryCode) !== -1;
  }

  let totalCount = 0;
  let chain = Promise.resolve();

  const currentYear = new Date().getFullYear();
  const minYear = 1991;

  for (let year = currentYear; year >= minYear; year--) {
    (function (y) {
      chain = chain.then(function () {
        return loadYear(y)
          .then(function (html) {
            const items = parseListItems(html);
            const matches = items.filter(function (item) {
              return itemMatchesCategory(item, selectedCategory);
            });

            if (matches.length > 0) {
              totalCount += matches.length;

              const yearBlock = document.createElement("div");
              yearBlock.style.marginBottom = "1.5rem";

              const yearHeading = document.createElement("h3");
              yearHeading.textContent = "Year " + y;
              yearBlock.appendChild(yearHeading);

              const ol = document.createElement("ol");
              matches.forEach(function (item) {
                ol.appendChild(item.cloneNode(true));
              });

              yearBlock.appendChild(ol);
              results.appendChild(yearBlock);
            }
          })
          .catch(function () {
            // Skip missing year files
          });
      });
    })(year);
  }

  chain.then(function () {
    if (totalCount === 0) {
      const p = document.createElement("p");
      p.textContent = "No publication is presently listed under this category.";
      results.appendChild(p);
    }
  });
})();
</script>
