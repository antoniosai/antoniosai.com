+++
title = "antoniosai.com"
[data]
baseChartOn = 3
colors = ["#627c62", "#11819b", "#ef7f1a", "#4e1154"]
columnTitles = ["Section", "Status", "Author"]
fileLink = "content/projects.csv"
title = "Projects"
+++

<script src="https://cdn.jsdelivr.net/npm/typeit@7.0.4/dist/typeit.min.js"></script>

<center>

  <h1 class="multipleStrings" style="font-weight: bold; font-size: 56px;"></h1>

</center>

<br>
<br>

{{< block "grid-2" >}}
{{< column >}}

<!-- Compose is a lean `Hugo` domentation theme, inspired by [forestry.io](https://forestry.io/docs/welcome/). -->
<h1 class="multipleStrings" style="font-size: 56px;"></h1>
Thanks for visiting my online space! You can get a peek inside my life as a committed software engineer by visiting this page. I get a kick out of the beauty of innovation and the skill of problem-solving, whether it be writing exquisite code or deciphering complex algorithms.

This website acts as a blank canvas for my career; it's a place where my love of technology and my obsessive curiosity about the world of learning meet. You'll come across both my comprehensive profile and a selection of interesting articles that explore the always changing world of software development as you travel through these virtual halls, in addition to my full profile. On this virtual journey, I want you to join me as we delve into the worlds of coding, creativity, and ongoing development.

{{< button "/about-me" "See more About Me" >}}{{< button "/contact-me" "Contact Me" >}}
{{< /column >}}

{{< column >}}
<img src="https://upload.wikimedia.org/wikipedia/commons/6/6f/Programming123najra.gif" alt="A beautiful landscape" style="border-radius: 8px; align: center">

{{< /column >}}
{{< /block >}}

<script>
  new TypeIt(".multipleStrings", {
  strings: [
    "Welcome to My Personal Website", 
    "My Name Is Antonio Saiful Islam", 
    "I am a Software Engineer", 
    "Love to Code", 
    "Always Young and Passionate"
  ],
  breakLines: false,
  loop: true,
  speed: 50
  }).go();
</script>
