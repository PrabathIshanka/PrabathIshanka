### Hi Guys 👋	


![profile](https://user-images.githubusercontent.com/90409117/156692408-3f4aab37-fe07-4b06-bc8c-8887ae802ef6.gif)
	 
	 





### About Me

**Call me Prabath ❤️**	

                    -I know some kind of It technologies....
                    -Using That Knowledge , I build a my IT world.....
                    -I am developing applications..
                       - ✨ Software ✨ 
                       - ✨ Mobile ✨
                       - ✨ Web ✨
                    -  .... and do graphics as a Freelancer
	
                     -   ....Let's go and see it.....😄

### Languages
 


### Technologies	


![PrabathIshanka's GitHub stats](https://github-readme-stats.vercel.app/api?username=PrabathIshanka&theme=dark&show_icons=true)	

[![Top Langs](https://github-readme-stats.vercel.app/api/top-langs/?username=PrabathIshanka&layout=compact)](https://github.com/PrabathIshanka/github-readme-stats)

 
- ⚡ Fun fact: I don't Like Fun fact..😄



require("dotenv").config();
const {
  renderError,
  parseBoolean,
  parseArray,
  clampValue,
  CONSTANTS,
} = require("../src/common/utils");
const fetchStats = require("../src/fetchers/stats-fetcher");
const renderStatsCard = require("../src/cards/stats-card");
const blacklist = require("../src/common/blacklist");
const { isLocaleAvailable } = require("../src/translations");

module.exports = async (req, res) => {
  const {
    username,
    hide,
    hide_title,
    hide_border,
    hide_rank,
    show_icons,
    count_private,
    include_all_commits,
    line_height,
    title_color,
    icon_color,
    text_color,
    bg_color,
    theme,
    cache_seconds,
    custom_title,
    locale,
    disable_animations,
    border_radius,
    border_color,
  } = req.query;
  res.setHeader("Content-Type", "image/svg+xml");

  if (blacklist.includes(username)) {
    return res.send(renderError("Something went wrong"));
  }

  if (locale && !isLocaleAvailable(locale)) {
    return res.send(renderError("Something went wrong", "Language not found"));
  }

  try {
    const stats = await fetchStats(
      username,
      parseBoolean(count_private),
      parseBoolean(include_all_commits),
    );

    const cacheSeconds = clampValue(
      parseInt(cache_seconds || CONSTANTS.TWO_HOURS, 10),
      CONSTANTS.TWO_HOURS,
      CONSTANTS.ONE_DAY,
    );

    res.setHeader("Cache-Control", `public, max-age=${cacheSeconds}`);

    return res.send(
      renderStatsCard(stats, {
        hide: parseArray(hide),
        show_icons: parseBoolean(show_icons),
        hide_title: parseBoolean(hide_title),
        hide_border: parseBoolean(hide_border),
        hide_rank: parseBoolean(hide_rank),
        include_all_commits: parseBoolean(include_all_commits),
        line_height,
        title_color,
        icon_color,
        text_color,
        bg_color,
        theme,
        custom_title,
        border_radius,
        border_color,
        locale: locale ? locale.toLowerCase() : null,
        disable_animations: parseBoolean(disable_animations),
      }),
    );
  } catch (err) {
    return res.send(renderError(err.message, err.secondaryMessage));
  }
};
