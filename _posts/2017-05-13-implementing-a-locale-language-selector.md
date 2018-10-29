---
ID: 1213
post_title: >
  Implementing A Locale / Language
  Selector
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/implementing-a-locale-language-selector/
published: true
post_date: 2017-05-13 14:52:18
---
<h2>The Problem</h2>
Android applications are distributed to users around the world, and these users aren't always going to speak the same languages as you. Luckily, Android has <a href="https://developer.android.com/guide/topics/resources/localization.html" target="_blank" rel="noopener noreferrer">excellent built-in support</a> for automatically applying the right language, however this isn't always enough. Sometimes a user may want to choose their language, and unfortunately there's no built-in way to support this. The game <a href="https://play.google.com/store/apps/details?id=uk.co.jakelee.blacksmith" target="_blank" rel="noopener noreferrer">Pixel Blacksmith</a> uses the technique described in this article.<!--more-->
<h2>The Solution</h2>
To allow changing language, the Locale needs to be overridden and set to the user's choice. How the user selects a language isn't too important, in this example a spinner was chosen since it was the easiest to work with. This choice is stored as a shared preference that is then applied every time the application launches.
<h4>On Startup</h4>
Inside the application class, or early on in the main activity, a call to <code>LanguageHelper.updateLanguage()</code> is made, which will handle the locale overwriting. It is passed the application context, which is required to access shared preferences and modify the locale later on.

This function looks up the saved language from shared preferences, then passes that to the main <code>updateLanguage()</code> function, described in the next section.
<pre>
public static void updateLanguage(Context ctx) {
    String lang = PreferenceManager.getDefaultSharedPreferences(ctx).getString("locale", "");
    updateLanguage(ctx, lang);
}</pre>
<h4>Changing Locale</h4>
When a context and 2 character locale (e.g. "en" for English or "es" for Spanish) are passed, this new locale is saved in shared preferences. Then, so long as the language string isn't empty, a new Locale is created using it, and this Locale is applied using <code>updateConfiguration()</code>. Note that this method now appears to be deprecated, and it <a href="http://stackoverflow.com/questions/40221711/android-context-getresources-updateconfiguration-deprecated/40704077#40704077" target="_blank" rel="noopener noreferrer">doesn't look like a quick fix</a>, but in your case it may be worth implementing.
<pre>
private static void updateLanguage(Context ctx, String lang) {
    PreferenceManager.getDefaultSharedPreferences(ctx).edit().putString("locale", lang).apply();
    Configuration cfg = new Configuration();
    if (!TextUtils.isEmpty(lang)) {
        cfg.locale = new Locale(lang);
    } else {
        cfg.locale = Locale.getDefault();
    }

    ctx.getResources().updateConfiguration(cfg, null);
}</pre>
<h4>Selecting A Language</h4>
When a language from the spinner is selected, the following <code>onItemSelected()</code> method is called. Most of the code is not specific to this example, but the important line is the <code>LanguageHelper.updateLanguage()</code> method, which is passed a context, and the position selected (+1 to account for zero indexing), which refers to a predefined language ID.
<pre>
public void onItemSelected(AdapterView parentView, View selectedItemView, int position, long id) {
    setting.setIntValue(position + 1);
    setting.save();

    LanguageHelper.updateLanguage(activity, position + 1);
    ToastHelper.showPositiveToast(parentView, ToastHelper.SHORT, parentView.getSelectedItem().toString(), true);
    onResume();
}</pre>
This <code>updateLanguage()</code> wrapper needs to look up the locale string from the ID / position passed. The numbers are sequential for ease of us in this example.
<pre>
public static void updateLanguage(Context context, int language) {
    updateLanguage(context, getLocaleById(language));
}

private static String getLocaleById(int id) {
    switch (id) {
        case Constants.LANG_ENGLISH:
            return "en";
        case Constants.LANG_FRENCH:
            return "fr";
        ... more languages omitted ...
        case Constants.LANG_GERMAN:
            return "de";
    }
    return "";
}</pre>
Once a language string is obtained, the normal <code>updateLanguage()</code> function can be used, and the language is set immediately, and will be automatically reapplied when the application restarts.
<h2>The Conclusion</h2>
Localising applications is essential if users who don't speak your language are to be engaged. Giving the users the ability to choose their own language gives them an extra element of control, which many will appreciate. It also helps when testing translations during development, as languages can be easily switched, without requiring changing the system Locale, or starting a new emulator.

All code used in this article is <a href="https://gist.github.com/JakeSteam/4ee40b6df4e30a968231d38faa2f26de" target="_blank" rel="noopener noreferrer">available as a Gist</a>.

Some of the code is modified from a <a href="http://stackoverflow.com/a/23351558/608312" target="_blank" rel="noopener">StackOverflow answer</a>.