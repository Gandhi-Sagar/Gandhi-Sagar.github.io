---
title: 'Kinect V2 Speech Basics D2D Visual Studio Patch C++'
author: Sagar Gandhi
layout: post
permalink: /Kinect-V2-Speech-Basics-D2D-Visual-Studio-Patch
categories:
  - Posts
---

<!--excerpt.start-->If you are working on Kinect for Windows V2 and want to play with Speech Basics C++ Sample, and you are using Visual Studio Express Edition, this post is for you.  
  
Recently when working with kinect, I came accross interesting issue. I was using Visual Studio Express Edition, I installed Speech-Basics-D2D C++ sample, build and boom.. build failed with an error:
{% highlight c %}
sphelper.h(46): fatal error C1083: Cannot open include file: 'atlbase.h': No such file or directory
{% endhighlight %}
After searching through, I found that atlbase.h is specific to ATL/MFC and in order to remove this dependency we need to intall non-express version of Visual Studio. There were worarounds - atlbase.h used to come with Windows Driver Kit Version 7.1.0 or with platform SDK. But apparantly, it is ommited from current versions of both.  
So we need to install Professional edition of Visual Studio (which may not be very economic) or copy some files from other computers, in which case there is no gaurantee that things will work out smoothly. So I present a cleaner workaround.  
<!--excerpt.end-->  <a class="anchor" id="read-more"></a>

+ First of all install Speech-Basics - C++ Sample from Kinect for Windows V2 Sample Browser. Go to installed location and open the solution with Visual Studio.  
+ Files of our interest are SpeechBasics.h and SpeechBasics.cpp, comment/remove `#include <atlbase.h>` and add a new line - `#include <sapi.h>`  
+ Now in SpeechBasics.cpp, search for "SpFindBestToken" and comment out the entire block as listed below:  
      {% highlight c %}
  // hr = SpFindBestToken(SPCAT_RECOGNIZERS, L"Language=409;Kinect=True", NULL, &pEngineToken);
  // if (SUCCEEDED(hr))
  // {
  //     m_pSpeechRecognizer->SetRecognizer(pEngineToken);
  //     hr = m_pSpeechRecognizer->CreateRecoContext(&m_pSpeechContext);
  
  // For long recognition sessions (a few hours or more), it may be beneficial to turn off adaptation of the acoustic model. 
  // This will prevent recognition accuracy from degrading over time.
  //     if (SUCCEEDED(hr))
  //     {
  //         hr = m_pSpeechRecognizer->SetPropertyNum(L"AdaptationOn", 0);                
  //     } 
      {% endhighlight %}
+ Now, just above this commented block, add a new block listed below: 
      {% highlight cpp %}
      
  // first, get pointer to SpObjectTokenCategory
  ISpObjectTokenCategory *pObjectTokenCategory = NULL;
  hr = CoCreateInstance(CLSID_SpObjectTokenCategory, NULL, CLSCTX_INPROC_SERVER, __uuidof(ISpObjectTokenCategory), (void**)&pObjectTokenCategory);
  if (SUCCEEDED(hr))
  {
	// Over pObjectTokenCategory, call setID, EnumObjects to get pointer to EnumSpObjectTokens
	  hr = pObjectTokenCategory->SetId(SPCAT_RECOGNIZERS, FALSE);
	  if (SUCCEEDED(hr))
	  {
		  IEnumSpObjectTokens		*pEnumSpObjectTokens = NULL;
		  hr = pObjectTokenCategory->EnumTokens(L"Language=409;Kinect=True", NULL, &pEnumSpObjectTokens);
		  if (SUCCEEDED(hr))
		  {
			// now we are in position to ask for SpTokenObject
			hr = pEnumSpObjectTokens->Next(1, &pEngineToken, NULL);
			if (SUCCEEDED(hr))
			{
			   if (hr == S_FALSE)
			   {
				  hr = E_UNEXPECTED;
				  ReleaseInterface((IUnknown*)pEnumSpObjectTokens);
				  ReleaseInterface((IUnknown*)pObjectTokenCategory);
				  return hr;
			   }
			   if (SUCCEEDED(hr))
			   {
				  pSpRecognizer->SetRecognizer(pEngineToken);
				  hr = pSpRecognizer->CreateRecoContext(&pSpRecoContext);
				  // For long recognition sessions (a few hours or more), it may be beneficial to turn off adaptation of the acoustic model. 
				  // This will prevent recognition accuracy from degrading over time.
				  if (SUCCEEDED(hr))
				  {
					 hr = pSpRecognizer->SetPropertyNum(L"AdaptationOn", 0);
				  }
			   }
			}
		  }
		  ReleaseInterface((IUnknown*)pEnumSpObjectTokens);
	  }
	}
	ReleaseInterface((IUnknown*)pObjectTokenCategory);
      {% endhighlight %}
+ Why does this work? Find in subpoints below:
  - I searched around a little and found that SpFindBestToken is helper function for enumerating a token and finding best match as per provided attributes(Language=409;Kinect=True here).
  - As it is a helper function, we can simply use its body directly. So what SpFindBestToken does is: validate the attributes, and calls SpEnumTokens.
  - Now SpEnumTokens uses CComPtr - a smart pointer class for managing COM interface. So, Speech-Basics-D2D is NOT using ATL/MFC, it is simply using CComPtr and thats why we needed atlbase header file. ATL also uses CComPtr is a different thing.
  - So concisely, what is needed is - unrolling helper functions, without using CComPtr.
  - A little about getting this to work, ISpObjectTokenCategory has EnumTokens method. Before we call it, we  must set identifier. There are multiple catagories we can work with. We are interested in Recognizers only.
  - Then calling EnumObjects and then next on valid EnumObjectToken pointer to get best matching token.  Essentially we checking return value for S_FALSE. This will be the case when no suitable token found, cause could be not installing Speech SDK or similar.
  - Then doing the things which are already present in Sample i.e. getting valid pointer to ISpRecoContext. Finally, we are releasing IEnumObjectTokens pointer which is very importent.
+ Hit F7 and you should be good to go.  
  
If you have any questions, have any problems getting this to work, mail me on gandhi.sagar.gs@gmail.com and I would be glad to help you.  
If you ever use this, if possible, drop me a mail, I would like to know how many people are actually using it.

