---
title: Converting mp3s to ogg files in the background with Sidekiq and FFmpeg
date: 2013-04-25
tags: 
  - ruby
  - rails
  - devops
alias: 128-converting-mp3s-ogg-files-background-sidekiq-and-ffmpeg
---

I have a friend for whom I'm building a site right now, and I chose Rails to do so. I think I'll probably reach for Rails for most sites I build until I get bored of it, which isn't going to happen any time soon. I also learned a few things about different browser's implementations of HTML5 audio, which I'll get into first.

My buddy is in a band, and so part of the functionality of the site is a photo gallery, and another part is a music player. Whereas in the past I'd have just reached for something off the shelf like [jPlayer](http://www.jplayer.org/), I decided to go the HTML5 route this time, as I was fairly confident that my buddy wouldn't be asking for legacy browser support. In any event, he's not paying for legacy browser support, so I decided to teach myself a few long overdue tricks.

First up is a bit of a primer on the HTML Audio element. I found, as usual, Mozilla to have the most understandable and trustworthy documentation - [here](https://developer.mozilla.org/en-US/docs/HTML/Element/audio), and [here](https://developer.mozilla.org/en-US/docs/HTML/Using_HTML5_audio_and_video). Building the player was fairly straightforward, but required a bunch of repetitive code that I'm too embarrassed to post here. It's fairly simple to create an audio element, list out a bunch of song objects with recording attributes attached to them, set a `data-source` attribute on those songs that points to the path where your uploaded recording file lives, courtesy of the Carrierwave and jQuery FileUpload gems. When you click on one of the songs, it kicks off the player.play() method and your song plays.

The surprise was that Firefox, in which I work every day, doesn't like mp3 files. There's some contradictory info out there about whether or not FF does or doesn't support mp3, but my version does not, so I had to figure out how to get an ogg version of the file up there also.

The method I came up with was to install FFmpeg to do the conversion, but to place the conversion into a background Sidekiq job so it didn't hang up the browser when my buddy uploaded his song. Sidekiq makes this so absurdly easy, and the [Railscast](http://railscasts.com/episodes/366-sidekiq) steps you right through the process. Basically any processing that you'd want to do in the `create` or `update` action in your controller can be moved into a Sidekiq worker that's called instead of doing the processing synchronously. Watch - 

~~~ruby
# songs#create

def create
  @song = Song.new(params[:song])
  @song.title = @song.default_name unless @song.title
  if @song.save
    ConversionWorker.perform_async(@song.id)
    #logger.debug path
    redirect_to music_path
  end
end
~~~

And the worker --

~~~ruby
# app/workers/conversion_worker.rb

class ConversionWorker
  include Sidekiq::Worker

  def perform(song_id)
    song = Song.find song_id
    path = "#{Rails.root}/public#{song.recording_url}"
    ogg = path.gsub(/mp3$/, "ogg")
    yup = %x[ffmpeg -i #{path} -acodec libvorbis #{ogg}]
  end
end
~~~

Converting stuff with FFmpeg was really straightforward, but only in Ubuntu. I fought with trying to get it set up with libvorbis on the Mac and eventually gave up. `%x[]` is the easiest way I found to execute shell commands from Ruby, complete with string interpolation. Basically this says - load that song, give me the recording_url (convenient that these Carrierwave methods are in scope), and create an ogg version. Do that by putting it right next to the mp3 version, but with the ogg file extension. 