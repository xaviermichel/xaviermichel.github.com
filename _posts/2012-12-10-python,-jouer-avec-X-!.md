---
layout: post
category : premiers pas
tagline: ""
tags : [python, x]
---
{% include JB/setup %}

Le but de cet article est de me fournir un aide mémoire sur la manipulation du serveur X avec python.

Dans ce code, je n'ai tenté que de jouer avec la souris mais cela m'avait permis de faire un bot qui répétait à l'infini une action que je n'avais faite qu'une fois.

*****

Le code parle de lui-même, il s'agit plus d'un aide mémoire que d'autre chose...

    import Xlib.display
    import Xlib.ext.xtest
     
    class MouseControl:
    	def __init__(self):
    		self.display = Xlib.display.Display()
    		self.screen = self.display.screen()
    		self.root = self.screen.root
     
    	def mouse_click(self, button): #button= 1 left, 2 middle, 3 right
    		self.mouse_down(button)
    		self.mouse_up(button)
     
    	def get_screen_resolution(self):
    		return self.screen['width_in_pixels'], self.screen['height_in_pixels']
     
    	def get_mouse_position(self) :
    		data = self.screen.root.query_pointer()._data
        		return data["root_x"], data["root_y"]
     
    	def mouse_down(self, button): 
    		Xlib.ext.xtest.fake_input(self.display, Xlib.X.ButtonPress, button)
    		self.display.sync()
     
    	def mouse_up(self, button):
    		Xlib.ext.xtest.fake_input(self.display, Xlib.X.ButtonRelease, button)
    		self.display.sync() 
     
    	def mouse_warp(self, x,y):
    		self.root.warp_pointer(x,y)
    		self.display.sync()
     
     
     
    mc = MouseControl()
    print mc.get_mouse_position()
    mc.mouse_warp(0, 0)
    mc.mouse_click(1)

