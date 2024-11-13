# How I made this

Oliver gave me an excel spreadsheet with album data in it, exported from
delicious library.

## Cleaning up the data

I opened the spreadsheet in Numbers and then exported it as a single CSV
file. I manually removed blank lines and duplicated heading lines (after
verifying that they were duplicates). I planned to strip out some fields
using `awk` but realised fairly quickly that this wouldn't work well
because some columns are quoted with embedded commas.

Instead I figured that there must be a better tool for grabbing stuff out
of a CSV file, and went looking for one in homebrew:

```
brew search csv
```

I looked at a couple and decided that `csvkit`
([docs](https://csvkit.readthedocs.io/en/latest/)) looked best (but `qsv`
([docs](https://github.com/jqnatividad/qsv/blob/master/README.md)) is worth
future exploration!).  With that installed (`brew install csvkit`) I played
around a little and came up with this to extract the desired columns and
convert them to a JSON array:

```
csvcut -c creator,title,Shelf records_full_list.csv | csvjson > albums.json
```

## Turning it into a web page

Now that I had the data in a usable format I "cheated" with ChatGPT. I
expected this to take a little bit of back and forth but it was incredible
simple. I asked:

> I have a json array containing a list of record albums in this format:
>
> [{"creator": null, "title": "Fantasia", "Shelf": "Records I've Purchased"}, {"creator": null, "title": "Feel The Beat", "Shelf": "Records I've Purchased"}, {"creator": null, "title": "Hair", "Shelf": "Records I've Purchased"}, {"creator": null, "title": "Jesus Christ Superstar", "Shelf": "Records I've Purchased"}, {"creator": null, "title": "Tasmanian Military Tattoo", "Shelf": "Records I've Purchased"}, {"creator": null, "title": "The Golden Years 1925-1935 Dance Bands", "Shelf": "Records I've Purchased"}, {"creator": "208L Containers", "title": "A Night At The Mirage", "Shelf": "Records I've Purchased"}, {"creator": "ABBA", "title": "Gold", "Shelf": "Records I've Purchased"}]
>
> Please generate html and javascript for a web page which has a "what should I listen to?" button, and returns a random record from the list (formatted nicely in HTML).

I wanted to give it a small amount of real data to work with, enough that I
could easily test the result it gave back.

I ran into an issue I've seen previously where the result was truncated.
Assuming that this was probably due to the length, I said:

> Thanks, please try that again but omit the CSS &lt;style&gt; section for now.

The response looked ok at first glance. I copied it to a file and it worked
first go. I restored the CSS block from the initial response and it styled
nicely with no other changes. Finally, I loaded the complete `albums.json`
I had generated (above) inline instead of the example data, and had a
complete working example.

Here is [the conversation with
ChatGPT](https://chatgpt.com/share/673416a9-36e4-8000-9f8d-36ade6e1e639).

I was interested to see that the formatted output labels the contents of the
"creator" field as "Artist", which I didn't ask for but is a change I would have
made myself!

To make this available on the internet I first tried a Github Gist, but it
wouldn't render. Instead I used Github Pages, which has a
[walkthrough](https://pages.github.com/) on their landing page. This just
required creating a new repo and adding the file as `index.html`. 
