from kivy.config import Config
Config.set('graphics', 'fullscreen', 'auto')

import random
import numpy as np
import string
from kivy.app import App
from kivy.uix.boxlayout import BoxLayout
from kivy.uix.button import Button
from kivy.uix.label import Label
from kivy.uix.slider import Slider
from kivy.uix.textinput import TextInput
from kivy.graphics import Color, Line, Ellipse, Rectangle, InstructionGroup
from kivy.uix.widget import Widget
from kivy.properties import ListProperty
from kivy.uix.popup import Popup
from kivy.uix.screenmanager import ScreenManager, Screen
from kivy.uix.label import CoreLabel
from kivy.uix.image import Image
from kivy.uix.gridlayout import GridLayout
import networkx as nx
import pygame
from kivy.clock import Clock
import time

pygame.init()
    
def apply_button_style(button, bg_color, text_color=(1, 1, 1, 1)):
    with button.canvas.before:
        Color(*bg_color)  # Set the background color
        rect = Rectangle(pos=button.pos, size=button.size)
    # Bind size and position to update the rectangle accordingly
    button.bind(size=lambda instance, value: setattr(rect, 'size', instance.size))
    button.bind(pos=lambda instance, value: setattr(rect, 'pos', instance.pos))
    button.color = text_color  # Set the text color

def create_styled_button(text, size_hint_x=0.1, on_release=None):
    button = Button(
        text=text,
        size_hint_x=size_hint_x,
        background_normal='',  # Disables the default background
        background_color=(0.9, 0.521, 0.206, 1)  # Set your desired color in RGBA
    )
    if on_release:
        button.bind(on_release=on_release)
    return button

def create_title_button(text, size_hint_x=0.1, on_release=None, font_size='20sp'):
    button = Button(
        text=text,
        size_hint_x=size_hint_x,
        font_size=60,
        background_normal='',  # Disables the default background
        background_color=(0.90, 0.92, 0.94, 1),  # Set your desired color in RGBA
        color=(0.06, 0.08, 0.10, 1)  # Set text color to black
    )
    if on_release:
        button.bind(on_release=on_release)
    return button

def generate_random_word(length):
    vowels = 'aeiou'
    consonants = ''.join(set(string.ascii_lowercase) - set(vowels))

    result = []
    # Start with a random choice between consonant or vowel
    if random.choice([True, False]):
        result.append(random.choice(consonants).upper())  # Start with an uppercase letter
    else:
        result.append(random.choice(vowels).upper())  # Start with an uppercase letter

    space_added = False  # A flag to track if a space has been added
    while len(result) < length:
        last_is_vowel = result[-1].lower() in vowels  # Check if the last character was a vowel

        # Choose the next character type with weighted probability
        if last_is_vowel:
            next_char = random.choices(
                [random.choice(consonants), random.choice(vowels)],
                weights=[0.8, 0.2],  # 80% chance for consonant, 20% for another vowel
                k=1
            )[0]
        else:
            next_char = random.choices(
                [random.choice(vowels), random.choice(consonants)],
                weights=[0.8, 0.2],  # 80% chance for vowel, 20% for another consonant
                k=1
            )[0]

        # Add a space with a 20% chance after the 8th character but not in the last two characters of the word
        if len(result) >= 8 and len(result) < length - 2 and random.random() < 0.2:
            if not space_added or (space_added and result[-1] != ' '):  # Ensure no consecutive spaces
                result.append(' ')
                next_char = next_char.upper()  # Capitalize the character following the space
                space_added = True  # Set the flag since a space is added

        result.append(next_char)
        if result[-2] == ' ':  # If a space was just added
            result[-1] = result[-1].upper()  # Ensure the next character is capitalized

    return ''.join(result)

def generate_station_name():
    elements = ["Square", "Center", "Market", "New", "South", "North", "East", "West", "", "", "", "", "", "", ""]
    random_length = random.randint(4, 15)
    random_word = generate_random_word(random_length)

    # Choose whether to append an element prefix always, instead of sometimes having it alone.
    prefix = random.choice(elements)
    # Always append a randomly generated word to the chosen element.
    return prefix + " " + random_word

class MainMenu(Screen):
    def __init__(self, **kwargs):
        super(MainMenu, self).__init__(**kwargs)
        
        with self.canvas.before:
            Color(0.004, 0.118, 0.176, 1)  # RGBA for #011e2d
            self.bg_rect = Rectangle(size=self.size, pos=self.pos)
            self.bind(size=self._update_bg_rect, pos=self._update_bg_rect)
        
        layout = BoxLayout(orientation='vertical', padding=10, spacing=10)


        # Add the game logo at the top half
        logo = Image(
            source=r'C:\Users\User\Documents\Python\Metro_Game\My_Metro_Madness_Logo.png',
            size_hint=(1, 0.5),
            allow_stretch=True,
            keep_ratio=True
        )
        layout.add_widget(logo)

        # Create a grid layout for the buttons
        button_grid = GridLayout(cols=2, size_hint=(1, 0.5), spacing=10)

        play_main_button = create_title_button("Play Main Game", size_hint_x=1, on_release=self.play_main_game)
        button_grid.add_widget(play_main_button)

        play_sandbox_button = create_title_button("Play Sandbox", size_hint_x=1, on_release=self.play_sandbox)
        button_grid.add_widget(play_sandbox_button)

        controls_button = create_title_button("Controls", size_hint_x=1, on_release=self.show_controls)
        button_grid.add_widget(controls_button)

        quit_button = create_title_button("Quit Game", size_hint_x=1, on_release=self.quit_game)
        button_grid.add_widget(quit_button)

        layout.add_widget(button_grid)
        self.add_widget(layout)

    def _update_bg_rect(self, *args):
        self.bg_rect.size = self.size
        self.bg_rect.pos = self.pos

    def play_main_game(self, instance):
        self.manager.current = 'main_game'

    def play_sandbox(self, instance):
        self.manager.current = 'sandbox'

    def show_controls(self, instance):
        self.manager.current = 'controls'

    def quit_game(self, instance):
        App.get_running_app().stop()

class ControlsScreen(Screen):
    def __init__(self, **kwargs):
        super(ControlsScreen, self).__init__(**kwargs)
        
        with self.canvas.before:
            Color(0.004, 0.118, 0.176, 1)  # RGBA for #011e2d
            self.bg_rect = Rectangle(size=self.size, pos=self.pos)
            self.bind(size=self._update_bg_rect, pos=self._update_bg_rect)
        
        layout = BoxLayout(orientation='vertical', padding=10, spacing=10)

        controls_text = Label(
            text="Controls:\n\n- Add Station: Click 'Add Station'\n- Draw Line: Click 'Draw Line' and use Color Wheel for line color\n"
                 "- Deselect: Click 'Deselect'\n- Add Water: Click 'Add Water'\n- Add Park: Click 'Add Park'\n"
                 "- Erase Segments: Click 'Erase Segments'",
            font_size='24sp'
        )
        
        layout.add_widget(controls_text)

        back_button = create_title_button("Back to Main Menu", size_hint_x=1, on_release=self.back_to_main, font_size='40sp')
        layout.add_widget(back_button)

        self.add_widget(layout)

    def _update_bg_rect(self, *args):
        self.bg_rect.size = self.size
        self.bg_rect.pos = self.pos

    def back_to_main(self, instance):
        self.manager.current = 'main_menu'

class ColorBox(BoxLayout):
    selected_color = ListProperty([1, 0, 0, 1])  # Default to red

    def __init__(self, **kwargs):
        super(ColorBox, self).__init__(**kwargs)
        self.orientation = 'horizontal'
        self.colors = [
            [1, 0, 0, 1],  # Red
            [0, 1, 0, 1],  # Green
            [0, 0, 1, 1],  # Blue
            [1, 1, 0, 1],  # Yellow
            [1, 0, 1, 1],  # Magenta
            [0, 1, 1, 1],  # Cyan
            [0.5, 0.5, 0.5, 1],  # Gray
            [1, 1, 1, 1],  # White
            [1, 0.5, 0, 1],  # Orange
            [0.5, 0, 0.5, 1],  # Purple
            [0.6, 0.3, 0.2, 1],  # Brown
            [1, 0.75, 0.8, 1],  # Pink
            [0, 0, 0.5, 1]  # Navy
        ]
        for color in self.colors:
            btn = Button(background_color=color, size_hint=(1, 1))
            btn.bind(on_release=self.on_color_button_release)
            self.add_widget(btn)

    def on_color_button_release(self, button):
        self.selected_color = button.background_color

class MainGameScreen(Screen):
    money = 10000000000  # Initial money value
    def __init__(self, **kwargs):
        super(MainGameScreen, self).__init__(**kwargs)
        self.money_label = None  # Initialize the money label as None
        self.time_speed = 0.0  # Initial time speed multiplier
        self.is_running = True  # Variable to control game loop
        self.current_day = 1
        self.current_hour = 5
        self.current_minute = 0
        self.current_time_label = Label(
            text=self.get_current_time(),
            font_size='12sp',
            color=(1, 1, 1, 1),
            size_hint=(None, None),  # Remove size hints to use fixed size
            size=(150, 30),  # Set a fixed size for the label
            valign='middle',
            halign='center',
            pos=(self.width / 2 - 75, self.height / 2 + 100)  # Center horizontally and adjust vertically
        )
        self.current_time_label.bind(size=self.current_time_label.setter('text_size'))

        # New variables for financial status
        self.current_daily_expenses = 0
        self.current_loans = 0
        self.current_ticket_price = 2
        self.funds_received_from_city_government = 0
        self.funds_received_from_state_government = 0
        self.station_expenses = 0
        self.line_expenses = 0

        # Schedule the daily deduction method
        self.schedule_daily_expense_deduction()
        

        with self.canvas.before:
            Color(0.004, 0.118, 0.176, 1)  # Set the desired RGBA color here
            self.bg_rect = Rectangle(size=self.size, pos=self.pos)
            self.bind(size=self._update_bg_rect, pos=self._update_bg_rect)
        
        layout = BoxLayout(orientation='vertical', padding=5, spacing=5)

        controls_layout = GridLayout(cols=6, size_hint_y=0.1, spacing=10, padding=[0, 10, 0, 0])

        # Add the buttons to the controls layout
        line_button = create_styled_button("Connect Metro Line", on_release=self.on_line_button_click)
        controls_layout.add_widget(line_button)

        demolish_button = create_styled_button("Demolish Metro Line", on_release=self.on_demolish_button_click)
        controls_layout.add_widget(demolish_button)

        spacing_widget = create_styled_button("Click For Line Color ->", on_release=self.on_line_button_click)
        controls_layout.add_widget(spacing_widget)

        color_wheel_layout = BoxLayout(orientation='vertical', size_hint_x=0.1, size_hint_y=1)
        self.color_wheel = ColorBox(size_hint_y=1)  # Increase the height of the ColorBox
        color_wheel_layout.add_widget(self.color_wheel)
        controls_layout.add_widget(color_wheel_layout)

        # Add the money display to the controls layout
        money_layout = self.create_money_display()
        controls_layout.add_widget(money_layout)

        # Add the current time label to the controls layout
        controls_layout.add_widget(self.current_time_label)

        layout.add_widget(controls_layout)

        tool_layout = BoxLayout(size_hint_y=0.025, spacing=10)
        water_button = create_styled_button("Add Water", size_hint_x=0.2, on_release=self.on_water_button_click)
        tool_layout.add_widget(water_button)

        park_button = create_styled_button("Add Park", size_hint_x=0.2, on_release=self.on_park_button_click)
        tool_layout.add_widget(park_button)

        eraser_button = create_styled_button("Eraser", size_hint_x=0.2, on_release=self.on_eraser_button_click)
        tool_layout.add_widget(eraser_button)

        self.pen_size_slider = Slider(min=1, max=50, value=10, size_hint_x=0.4)
        tool_layout.add_widget(self.pen_size_slider)

        layout.add_widget(tool_layout)

        quit_to_main_button = create_styled_button("Main Menu", on_release=self.quit_to_main_menu)
        controls_layout.add_widget(quit_to_main_button)

        station_name_layout = BoxLayout(size_hint_y=0.03, spacing=10)
        station_name_layout.add_widget(Label(text="Station Name:", size_hint_x=0.05))
        self.station_name_input = TextInput(multiline=False, size_hint_x=0.05, font_size='12sp')
        station_name_layout.add_widget(self.station_name_input)

        add_station_button = create_styled_button("Add Named Station", size_hint_x=0.15, on_release=self.on_add_named_station_button_click)
        station_name_layout.add_widget(add_station_button)

        deselect_and_finance_layout = BoxLayout(size_hint_x=0.3, spacing=10)
        
        deselect_button = create_styled_button("Deselect", size_hint_x=0.15, on_release=self.on_deselect_button_click)
        deselect_and_finance_layout.add_widget(deselect_button)

        finance_button = create_styled_button("Finance", size_hint_x=0.15, on_release=self.show_finance_popup)
        deselect_and_finance_layout.add_widget(finance_button)
        
        station_name_layout.add_widget(deselect_and_finance_layout)

        layout.add_widget(station_name_layout)

        self.canvas_widget = CanvasWidget(size_hint_y=0.8, get_pen_size=self.get_pen_size, color_wheel=self.color_wheel)
        layout.add_widget(self.canvas_widget)

        clear_button = create_styled_button("Clear Canvas", on_release=self.on_clear_canvas_click)
        tool_layout.add_widget(clear_button)

        demolish_station_button = create_styled_button("Demolish Station", on_release=self.on_demolish_station_button_click)
        controls_layout.add_widget(demolish_station_button)

        demolish_label_button = create_styled_button("Demolish Label", on_release=self.on_demolish_label_button_click)
        controls_layout.add_widget(demolish_label_button)

        # Schedule the update function to run every frame
        Clock.schedule_interval(self.update, 1/60)

        # Add time control buttons
        pause_button = create_styled_button("Pause", on_release=self.pause_time)
        controls_layout.add_widget(pause_button)

        normal_speed_button = create_styled_button("Normal Speed", on_release=self.set_normal_speed)
        controls_layout.add_widget(normal_speed_button)

        fast_speed_button = create_styled_button("Fast Speed", on_release=self.set_fast_speed)
        controls_layout.add_widget(fast_speed_button)

        self.add_widget(layout)

    def _update_bg_rect(self, *args):
        self.bg_rect.size = self.size
        self.bg_rect.pos = self.pos

    def _update_bg_rect(self, *args):
        self.bg_rect.size = self.size
        self.bg_rect.pos = self.pos

    def create_money_display(self):
        money_layout = BoxLayout(size_hint=(None, 1), width=150)  # Set a fixed width for the money layout
        self.money_label = Label(text=self.format_money(self.money), font_size='12sp', color=(1, 1, 1, 1))
        money_layout.add_widget(Label(text="Money:", size_hint_x=None, width=50, font_size='12sp'))
        money_layout.add_widget(self.money_label)
        return money_layout
    
    def update_money_for_new_station(self):
        station_cost = 100000000  # $100 million
        if self.money >= station_cost:
            self.money -= station_cost
            self.update_money_display()
            return True
        else:
            self.show_insufficient_funds_popup(station_cost)
            return False
    
    def update_money_for_new_line(self, length):
        max_length = (self.width ** 2 + self.height ** 2) ** 0.5  # Diagonal length of the canvas
        max_cost = 5000000000  # $5 billion
        cost_per_unit_length = max_cost / max_length
        line_cost = length * cost_per_unit_length
        print(f"Line length: {length}, Line cost: {line_cost}")
        if self.money >= line_cost:
            self.money -= line_cost
            self.update_money_display()
            print(f"Money after deduction: {self.money}")
            return True
        else:
            self.show_insufficient_funds_popup(line_cost)
            return False
    
    def connect_stations(self, station1, station2):
        x1, y1 = station1[0], station1[1]
        x2, y2 = station2[0], station2[1]
        distance = np.sqrt((x2 - x1) ** 2 + (y2 - y1) ** 2)
        print(f"Connecting stations: {station1} -> {station2}, Distance: {distance}")
        self.update_money_for_new_line(distance)
        self.canvas_widget.draw_user_line((x1, y1), (x2, y2), self.canvas_widget.get_line_color())

        for station in [station1, station2]:
            if isinstance(station[2], Ellipse):
                self.canvas_widget.canvas.remove(station[2])
                with self.canvas_widget.canvas:
                    Color(0, 0, 0)
                    self.canvas_widget.canvas.add(station[2])

            if isinstance(station[3], Ellipse):
                self.canvas_widget.canvas.remove(station[3])
                with self.canvas_widget.canvas:
                    Color(1, 1, 1)
                    self.canvas_widget.canvas.add(station[3])
        self.canvas_widget.redraw_station_labels()

    def on_touch_up(self, touch):
        if self.canvas_widget.collide_point(*touch.pos) and self.canvas_widget.current_tool == 'line':
            if len(self.canvas_widget.selected_stations) == 2:
                self.connect_stations(self.canvas_widget.selected_stations[0], self.canvas_widget.selected_stations[1])
                self.canvas_widget.selected_stations.clear()
        return super(MainGameScreen, self).on_touch_up(touch)

    def on_touch_down(self, touch):
        if self.canvas_widget.collide_point(*touch.pos):
            if self.canvas_widget.current_tool in ['station', 'named_station']:
                self.update_money_for_new_station()
        return super(MainGameScreen, self).on_touch_down(touch)
    
    def show_insufficient_funds_popup(self, cost):
        content = BoxLayout(orientation='vertical', padding=10, spacing=10)
        formatted_cost = f"{cost:,.2f}"  # Format the cost with commas
        message = Label(text=f"Insufficient funds. Required: ${formatted_cost}", font_size='20sp', color=(1, 1, 1, 1))
        buttons = BoxLayout(size_hint_y=0.3, spacing=10)

        ok_button = create_styled_button("OK", size_hint_x=0.5)

        buttons.add_widget(ok_button)

        content.add_widget(message)
        content.add_widget(buttons)

        self.popup = Popup(title="Insufficient Funds", content=content, size_hint=(0.75, 0.5), auto_dismiss=False)

        # Customize the title color
        self.popup.title_color = [1, 1, 1, 1]  # Set the title color to white

        # Adding the orange line below the title
        with self.popup.canvas.before:
            Color(0.9, 0.521, 0.206, 1)  # Set the color to orange
            self.line = Rectangle(size=(self.popup.width, 2), pos=(self.popup.x, self.popup.y + self.popup.height))

        # Set background color
        with self.popup.canvas.before:
            Color(0.004, 0.118, 0.176, 1)  # Set the background color to #011e2d
            self.bg_rect = Rectangle(size=self.popup.size, pos=self.popup.pos)

        # Bind the position and size of the rectangle to the popup
        self.popup.bind(on_open=self.update_line_position)
        self.popup.bind(on_dismiss=self.remove_line)
        self.popup.bind(on_dismiss=self.remove_background)
        self.popup.bind(size=self.update_bg_rect, pos=self.update_bg_rect)

        ok_button.bind(on_release=self.popup.dismiss)
        self.popup.open()

    def update_line_position(self, *args):
        self.line.size = (self.popup.width, 2)
        self.line.pos = (self.popup.x, self.popup.y + self.popup.height)

    def remove_line(self, *args):
        self.popup.canvas.before.remove(self.line)

    def remove_background(self, *args):
        self.popup.canvas.before.remove(self.bg_rect)

    def update_bg_rect(self, *args):
        self.bg_rect.size = self.popup.size
        self.bg_rect.pos = self.popup.pos

    def on_add_named_station_button_click(self, instance):
        station_name = self.station_name_input.text
        self.canvas_widget.current_tool = 'named_station'
        self.canvas_widget.station_name = station_name

    def update(self, dt):
        if self.is_running:
            self.update_game_state(dt * self.time_speed)
    
    def update_game_state(self, time_passed):
        if self.is_running:
            total_minutes = int(time_passed * 15)  # Convert time_passed to minutes (1.0 time = 15 minutes)
            self.current_minute += total_minutes
            while self.current_minute >= 60:
                self.current_minute -= 60
                self.current_hour += 1
            while self.current_hour >= 24:
                self.current_hour -= 24
                self.current_day += 1
                # Deduct daily expenses at the end of each day
                self.deduct_daily_expenses()

            # Update the current time label
            self.current_time_label.text = self.get_current_time()
    
    def get_current_time(self):
        return f"Day {self.current_day}, {self.current_hour:02}:{self.current_minute:02}"

    def pause_time(self, instance):
        self.time_speed = 0
        self.is_running = False

    def set_normal_speed(self, instance):
        self.time_speed = 5.05
        self.is_running = True

    def set_fast_speed(self, instance):
        self.time_speed = 7.0
        self.is_running = True
    
    def update_daily_expenses(self):
        self.current_daily_expenses = self.station_expenses + self.line_expenses

    def deduct_daily_expenses(self, *args):
        # Deduct daily expenses
        total_daily_expenses = self.current_daily_expenses + self.station_expenses + self.line_expenses
        self.money -= total_daily_expenses
        self.update_money_display()
        print(f"Daily expenses deducted: {total_daily_expenses}. Current money: {self.money}")

    def schedule_daily_expense_deduction(self):
        # Calculate the time remaining until 23:59
        now = time.localtime()
        midnight = time.mktime((now.tm_year, now.tm_mon, now.tm_mday, 23, 59, 0, 0, 0, 0))
        time_until_midnight = midnight - time.mktime(now)

        # Schedule the first daily deduction event
        Clock.schedule_once(self.deduct_daily_expenses, time_until_midnight)

    def format_money(self, amount):
        if amount >= 1000000000:
            return f"${amount / 1000000000:.3f}B"
        elif amount >= 1000000:
            return f"${amount / 1000000:.3f}M"
        else:
            return f"${amount:.3f}"
        
    def other_format_money(self, amount):
        if amount >= 1000000000:
            return f"${amount / 1000000000:.2f}B"
        elif amount >= 1000000:
            return f"${amount / 1000000:.2f}M"
        else:
            return f"${amount:.2f}"


    def update_money_display(self):
        self.money_label.text = self.format_money(self.money)

    def show_finance_popup(self, instance):
        self.pause_time(None)

        content = BoxLayout(orientation='vertical', padding=10, spacing=10)

        # Display current money, prices and daily expenses
        current_money_label = Label(text=f"Current Money: {self.format_money(self.money)}", font_size='18sp')
        current_expenses_label = Label(text=f"Current Daily Expenses: {self.format_money(self.current_daily_expenses)}", font_size='18sp')

        # Placeholder buttons
        ask_city_button = create_styled_button("Ask City Government for Funding", size_hint_x=1, on_release=self.ask_city_funding)
        ask_state_button = create_styled_button("Ask State Government for Funding", size_hint_x=1, on_release=self.ask_state_funding)
        adjust_ticket_button = create_styled_button("Adjust Ticket Prices", size_hint_x=1, on_release=self.adjust_ticket_prices)
        take_loan_button = create_styled_button("Take Out a Loan", size_hint_x=1, on_release=self.take_loan)

        content.add_widget(current_money_label)
        content.add_widget(current_expenses_label)
        content.add_widget(ask_city_button)
        content.add_widget(ask_state_button)
        content.add_widget(adjust_ticket_button)
        content.add_widget(take_loan_button)

        self.finance_popup = Popup(title="Finance Options", content=content, size_hint=(0.75, 0.75), auto_dismiss=True)
        self.finance_popup.open()
        self.update_finance_labels() 
    
    def update_finance_labels(self):
        self.finance_popup.content.children[1].text = f"Current Ticket Price: {self.other_format_money(self.current_ticket_price)}"

    def ask_city_funding(self, instance):
        # Placeholder for future implementation
        pass

    def ask_state_funding(self, instance):
        # Placeholder for future implementation
        pass

    def show_ticket_price_slider_popup(self):
        content = BoxLayout(orientation='vertical', padding=10, spacing=10)
    
        # Create a label to show the current ticket price
        current_price_label = Label(text=f"Current Ticket Price: ${self.current_ticket_price:.2f}", font_size='18sp')

        # Create a slider for adjusting the ticket price
        ticket_price_slider = Slider(min=0, max=25, step=0.25, value=self.current_ticket_price)
    
        # Update the label text as the slider value changes
        def on_slider_value_change(instance, value):
            current_price_label.text = f"Current Ticket Price: ${value:.2f}"
            self.current_ticket_price = value  # Update the variable directly on value change

        ticket_price_slider.bind(value=on_slider_value_change)
    
        # Create a button to confirm the new ticket price
        confirm_button = create_styled_button("Confirm", size_hint_x=1, on_release=lambda x: self.confirm_ticket_price())
    
        content.add_widget(current_price_label)
        content.add_widget(ticket_price_slider)
        content.add_widget(confirm_button)
    
        self.ticket_price_popup = Popup(title="Adjust Ticket Prices", content=content, size_hint=(0.75, 0.5), auto_dismiss=True)
        self.ticket_price_popup.open()

    def confirm_ticket_price(self):
        print(f"New ticket price confirmed: ${self.current_ticket_price:.2f}")  # Debugging print statement
        self.ticket_price_popup.dismiss()
        if hasattr(self, 'finance_popup') and self.finance_popup:
            self.update_finance_labels()

    def set_new_ticket_price(self, new_price):
        self.current_ticket_price = new_price
        self.ticket_price_popup.dismiss()
        print(f"New ticket price set to: ${new_price:.2f}")

    def adjust_ticket_prices(self, instance):
        self.show_ticket_price_slider_popup()

    def take_loan(self, instance):
        self.show_loan_popup()

    def show_loan_popup(self):
        content = BoxLayout(orientation='vertical', padding=10, spacing=10)

        # Loan options and details
        loan_options = [
            {"amount": 5000000000, "principal": 100000000, "interest": 5000000},
            {"amount": 10000000000, "principal": 100000000, "interest": 10000000},
            {"amount": 20000000000, "principal": 100000000, "interest": 20000000},
        ]

        for option in loan_options:
            button_text = f"${option['amount'] / 1000000000:.1f}B Loan\n" \
                        f"Principal: ${option['principal'] / 1000000:.1f}M daily\n" \
                        f"Interest: ${option['interest'] / 1000000:.1f}M daily"
            loan_button = create_styled_button(button_text, size_hint_x=1, on_release=lambda x, opt=option: self.set_selected_loan(opt))
            content.add_widget(loan_button)

        # Confirm button to finalize the loan
        confirm_button = create_styled_button("Confirm", size_hint_x=1, on_release=self.confirm_selected_loan)
        content.add_widget(confirm_button)

        # Cancel button to return to the finance popup
        cancel_button = create_styled_button("Cancel", size_hint_x=1, on_release=self.cancel_loan)
        content.add_widget(cancel_button)

        self.selected_loan_option = None  # Initialize selected loan option
        self.loan_popup = Popup(title="Take Out a Loan", content=content, size_hint=(0.75, 0.75), auto_dismiss=True)
        self.loan_popup.open()

    def cancel_loan(self, instance):
        self.loan_popup.dismiss()
        self.show_finance_popup(None)

    def set_selected_loan(self, option):
        self.selected_loan_option = option

    def confirm_selected_loan(self, instance):
        if self.selected_loan_option:
            self.money += self.selected_loan_option['amount']
            self.current_daily_expenses += self.selected_loan_option['principal'] + self.selected_loan_option['interest']
            self.update_money_display()
        self.loan_popup.dismiss()

    def on_demolish_button_click(self, instance):
        self.canvas_widget.current_tool = 'demolish'

    def on_demolish_label_button_click(self, instance):
        self.canvas_widget.current_tool = 'demolish_label'

    def on_deselect_button_click(self, instance):
        self.canvas_widget.current_tool = None

    def on_water_button_click(self, instance):
        self.canvas_widget.current_tool = 'water'

    def on_clear_canvas_click(self, instance):
        self.canvas_widget.show_clear_confirmation()

    def on_park_button_click(self, instance):
        self.canvas_widget.current_tool = 'park'

    def on_eraser_button_click(self, instance):
        self.canvas_widget.current_tool = 'eraser'

    def get_pen_size(self):
        return self.pen_size_slider.value

    def on_line_button_click(self, instance):
        print("Line draw tool activated")
        self.canvas_widget.current_tool = 'line'
    
    def on_demolish_station_button_click(self, instance):
        self.canvas_widget.current_tool = 'demolish_station'

    def quit_to_main_menu(self, instance):
        self.manager.current = 'main_menu'

class SandboxScreen(Screen):
    def __init__(self, **kwargs):
        super(SandboxScreen, self).__init__(**kwargs)
        self.current_daily_expenses = 0
        self.current_loans = 0
        self.current_ticket_price = 2
        self.funds_received_from_city_government = 0
        self.funds_received_from_state_government = 0
        self.station_expenses = 0
        self.line_expenses = 0
        self.money = 9999999999999999999999
        self.money_label = None 
        
        
        with self.canvas.before:
            Color(0.004, 0.118, 0.176, 1)  # Set the desired RGBA color here
            self.bg_rect = Rectangle(size=self.size, pos=self.pos)
            self.bind(size=self._update_bg_rect, pos=self._update_bg_rect)

        layout = BoxLayout(orientation='vertical', padding=5, spacing=5)

        controls_layout = BoxLayout(size_hint_y=0.025, spacing=10)
        controls_layout.add_widget(Label(text="Enter number of lines:", size_hint_x=0.25))
        self.num_lines_input = TextInput(multiline=False, size_hint_x=0.02, font_size='12sp')
        controls_layout.add_widget(self.num_lines_input)

        generate_button = create_styled_button("Generate Lines", on_release=self.on_generate_button_click)
        controls_layout.add_widget(generate_button)

        line_button = create_styled_button("Connect Metro Line", size_hint_x=0.12, on_release=self.on_line_button_click)
        controls_layout.add_widget(line_button)

        demolish_button = create_styled_button("Demolish Metro Line", size_hint_x=0.12, on_release=self.on_demolish_button_click)
        controls_layout.add_widget(demolish_button)

        demolish_label_button = create_styled_button("Demolish Label", on_release=self.on_demolish_label_button_click)
        controls_layout.add_widget(demolish_label_button)

        spacing_widget = create_styled_button("Click For Line Color ->", size_hint_x=0.12, on_release=self.on_line_button_click)
        controls_layout.add_widget(spacing_widget)

        color_wheel_layout = BoxLayout(orientation='vertical', size_hint_x=0.1, size_hint_y=1)
        self.color_wheel = ColorBox(size_hint_y=1)  # Increase the height of the ColorBox
        color_wheel_layout.add_widget(self.color_wheel)
        controls_layout.add_widget(color_wheel_layout)

        layout.add_widget(controls_layout)

        tool_layout = BoxLayout(size_hint_y=0.025, spacing=10)
        water_button = create_styled_button("Add Water", size_hint_x=0.2, on_release=self.on_water_button_click)
        tool_layout.add_widget(water_button)

        park_button = create_styled_button("Add Park", size_hint_x=0.2, on_release=self.on_park_button_click)
        tool_layout.add_widget(park_button)

        eraser_button = create_styled_button("Eraser", size_hint_x=0.2, on_release=self.on_eraser_button_click)
        tool_layout.add_widget(eraser_button)

        self.pen_size_slider = Slider(min=1, max=50, value=10, size_hint_x=0.4)
        tool_layout.add_widget(self.pen_size_slider)

        layout.add_widget(tool_layout)

        quit_to_main_button = create_styled_button("Main Menu", on_release=self.quit_to_main_menu)
        controls_layout.add_widget(quit_to_main_button)

        station_name_layout = BoxLayout(size_hint_y=0.03, spacing=10)
        station_name_layout.add_widget(Label(text="Station Name:", size_hint_x=0.05))
        self.station_name_input = TextInput(multiline=False, size_hint_x=0.05, font_size='12sp')
        station_name_layout.add_widget(self.station_name_input)

        add_station_button = create_styled_button("Add Named Station", size_hint_x=0.15, on_release=self.on_add_named_station_button_click)
        station_name_layout.add_widget(add_station_button)

        deselect_button = create_styled_button("Deselect", size_hint_x=0.15, on_release=self.on_deselect_button_click)
        station_name_layout.add_widget(deselect_button)

        layout.add_widget(station_name_layout)

        self.canvas_widget = CanvasWidget(size_hint_y=0.8, get_pen_size=self.get_pen_size, color_wheel=self.color_wheel)
        layout.add_widget(self.canvas_widget)

        clear_button = create_styled_button("Clear Canvas", on_release=self.on_clear_canvas_click)
        tool_layout.add_widget(clear_button)

        demolish_station_button = create_styled_button("Demolish Station", on_release=self.on_demolish_station_button_click)
        controls_layout.add_widget(demolish_station_button)

        generate_roads_button = create_styled_button("Generate Roads", size_hint_x=0.15, on_release=self.on_generate_roads_click)
        station_name_layout.add_widget(generate_roads_button)

        self.add_widget(layout)
    
    def update_daily_expenses(self):
        self.current_daily_expenses = self.station_expenses + self.line_expenses
    
    def update_money_display(self):
        self.money_label.text = self.format_money(self.money)

    def _update_bg_rect(self, *args):
        self.bg_rect.size = self.size
        self.bg_rect.pos = self.pos

    def on_demolish_label_button_click(self, instance):
        self.canvas_widget.current_tool = 'demolish_label'

    def on_generate_button_click(self, instance):
        try:
            num_lines = int(self.num_lines_input.text)
            if num_lines <= 0:
                raise ValueError("Number of lines must be positive.")
            self.canvas_widget.clear_system_generated_content()
            self.canvas_widget.generate_and_plot_lines(num_lines)
        except ValueError as e:
            print(f"Error: {e}")

    def on_demolish_button_click(self, instance):
        self.canvas_widget.current_tool = 'demolish'
    
    def on_demolish_station_button_click(self, instance):
        self.canvas_widget.current_tool = 'demolish_station'

    def on_add_named_station_button_click(self, instance):
        station_name = self.station_name_input.text
        self.canvas_widget.current_tool = 'named_station'
        self.canvas_widget.station_name = station_name

    def on_deselect_button_click(self, instance):
        self.canvas_widget.current_tool = None

    def on_water_button_click(self, instance):
        self.canvas_widget.current_tool = 'water'

    def on_clear_canvas_click(self, instance):
        self.canvas_widget.show_clear_confirmation()

    def on_park_button_click(self, instance):
        self.canvas_widget.current_tool = 'park'

    def on_eraser_button_click(self, instance):
        self.canvas_widget.current_tool = 'eraser'

    def on_generate_roads_click(self, instance):
        self.canvas_widget.generate_grid_roads()
        print("Roads generation initiated")

    def get_pen_size(self):
        return self.pen_size_slider.value
    
    def format_money(self, amount):
        if amount >= 1000000000:
            return f"${amount / 1000000000:.3f}B"
        elif amount >= 1000000:
            return f"${amount / 1000000:.3f}M"
        else:
            return f"${amount:.3f}"

    def on_line_button_click(self, instance):
        print("Line draw tool activated")
        self.canvas_widget.current_tool = 'line'

    def quit_to_main_menu(self, instance):
        self.manager.current = 'main_menu'

class CanvasWidget(BoxLayout):
    def __init__(self, get_pen_size, color_wheel, **kwargs):
        super().__init__(**kwargs)
        self.color_wheel = color_wheel
        self.get_pen_size = get_pen_size
        self.current_tool = None
        self.user_lines_dict = {}  # Dictionary to track user-drawn lines
        self.water_drawings = []
        self.park_drawings = []
        self.stations = []
        self.selected_stations = []
        self.lines = []
        self.lines_instruction = InstructionGroup()
        self.user_lines = []  # List to keep user-drawn lines
        self.system_lines = []  # List to keep system-generated lines
        self.system_stations = []  # System-generated stations
        self.selected_demolish_stations = []
        self.heatmap_visible = False
        self.heatmap_instruction = InstructionGroup()
        self.station_name = ""
        self.graph = nx.Graph()
        self.build_graph()

        with self.canvas.before:
            Color(1, 1, 1, 1)
            self.bg_rect = Rectangle(size=self.size, pos=self.pos)

        self.bind(size=self.update_bg, pos=self.update_bg)

    def update_bg(self, *args):
        self.bg_rect.size = self.size
        self.bg_rect.pos = self.pos

    def update_bg(self, *args):
        self.bg_rect.size = self.size
        self.bg_rect.pos = self.pos

    def build_graph(self):
        for station in self.stations:
            self.graph.add_node(station['name'], pos=(station['x'], station['y']))

        for station1 in self.stations:
            for station2 in self.stations:
                if station1 != station2:
                    dist = self.distance(station1['x'], station1['y'], station2['x'], station2['y'])
                    if dist < 300:  # Example threshold
                        self.graph.add_edge(station1['name'], station2['name'], weight=dist)

    def clear_system_generated_content(self):
        for line in self.system_lines:
            if line in self.canvas.children:
                self.canvas.remove(line)
        self.system_lines.clear()

        for station in self.system_stations:
            x, y, outer_ellipse, inner_ellipse, *label_instructions = station
            if outer_ellipse in self.canvas.children:
                self.canvas.remove(outer_ellipse)
            if inner_ellipse in self.canvas.children:
                self.canvas.remove(inner_ellipse)
            for instruction in label_instructions:
                if instruction in self.canvas.children:
                    self.canvas.remove(instruction)
        self.system_stations.clear()

    def generate_grid_roads(self):
        button_positions = self.get_button_positions()

        # Define grid parameters
        num_main_roads = 5
        spacing_main_roads = self.width // (num_main_roads + 1)
        num_small_roads = 10
        spacing_small_roads = self.width // (num_small_roads + 1)

        # Generate main horizontal and vertical roads (highways and avenues)
        for i in range(1, num_main_roads + 1):
            x = i * spacing_main_roads
            y = i * spacing_main_roads

            if self.is_point_in_drawable_area(x, 0) and self.is_point_in_drawable_area(x, self.height):
                self.generate_road(x, 0, x, self.height, road_type='highway')
            if self.is_point_in_drawable_area(0, y) and self.is_point_in_drawable_area(self.width, y):
                self.generate_road(0, y, self.width, y, road_type='avenue')

        # Generate smaller roads within the grid
        for i in range(1, num_small_roads + 1):
            x = i * spacing_small_roads
            y = i * spacing_small_roads

            if self.is_point_in_drawable_area(x, 0) and self.is_point_in_drawable_area(x, self.height):
                self.generate_road(x, 0, x, self.height, road_type='road')
            if self.is_point_in_drawable_area(0, y) and self.is_point_in_drawable_area(self.width, y):
                self.generate_road(0, y, self.width, y, road_type='road')

        # Occasionally add bridges over water
        for _ in range(5):  # Adding a few bridges
            x1, y1 = random.randint(0, int(self.width)), random.randint(0, int(self.height))
            x2, y2 = random.randint(0, int(self.width)), random.randint(0, int(self.height))
            if self.is_road_over_water(x1, y1, x2, y2):
                self.generate_road(x1, y1, x2, y2, road_type='bridge')

        # Generate curved roads
        self.generate_curved_roads()

    def get_button_positions(self):
        button_positions = []
        for child in self.parent.children:
            if isinstance(child, Button):
                button_positions.append((child.pos, child.size))
        return button_positions

    def is_point_in_any_button(self, x, y, button_positions):
        for pos, size in button_positions:
            if pos[0] <= x <= pos[0] + size[0] and pos[1] <= y <= pos[1] + size[1]:
                return True
        return False
    
    def generate_road(self, x1, y1, x2, y2, road_type='road'):
        if not self.is_point_in_any_drawing((x1, y1), 50) and not self.is_point_in_any_drawing((x2, y2), 50):
            if self.is_road_crossing_park(x1, y1, x2, y2):
                return

            if self.is_road_over_water(x1, y1, x2, y2):
                if road_type != 'bridge' and not self.allow_road_bridge():
                    return

            if road_type in ['highway', 'avenue']:
                x2, y2 = self.extend_road_length(x1, y1, x2, y2, factor=2)

            self.draw_road(x1, y1, x2, y2, color=(0, 0, 0), width=1)

    def generate_road_type(self, road_type, width, length_factor):
        x1, y1 = random.uniform(0, self.width), random.uniform(0, self.height)
        angle = random.uniform(0, 2 * np.pi)
        length = random.uniform(self.width * 0.1, self.width * 0.2) * length_factor
        x2 = x1 + length * np.cos(angle)
        y2 = y1 + length * np.sin(angle)
        
        # Ensure the road is within the drawable area
        x2 = min(max(x2, 0), self.width)
        y2 = min(max(y2, 0), self.height)
        
        self.draw_road(x1, y1, x2, y2, color=(0, 0, 0), width=width)
        
        # Optionally, add curved sections
        if road_type in ['highway', 'main']:
            self.generate_curved_road(x1, y1, x2, y2, width)

    def generate_curved_road(self, x1, y1, x2, y2, width):
        ctrl_x1, ctrl_y1, ctrl_x2, ctrl_y2 = self.generate_control_points(x1, y1, x2, y2)
        bezier_points = self.calculate_bezier_points([(x1, y1), (ctrl_x1, ctrl_y1), (ctrl_x2, ctrl_y2), (x2, y2)])

        with self.canvas.before:
            Color(0, 0, 0)  # Set color to black
            for j in range(len(bezier_points) - 1):
                line = Line(points=[bezier_points[j][0], bezier_points[j][1], bezier_points[j + 1][0], bezier_points[j + 1][1]], width=width)
                self.canvas.add(line)

    def generate_curved_roads(self):
        num_curved_roads = 5  # Number of curved roads to generate
        for _ in range(num_curved_roads):
            x1, y1 = random.randint(0, int(self.width)), random.randint(0, int(self.height))
            x2, y2 = random.randint(0, int(self.width)), random.randint(0, int(self.height))
            width = 1  # Define the width for the curved roads
            self.generate_curved_road(x1, y1, x2, y2, width)

    def generate_realistic_roads(self):
        self.clear_system_generated_content()
        
        # Parameters
        num_highways = 3
        num_main_roads = 5
        num_secondary_roads = 10
        num_residential_streets = 20
        
        # Generate Highways
        for _ in range(num_highways):
            self.generate_road_type('highway', 5, 2)
        
        # Generate Main Roads
        for _ in range(num_main_roads):
            self.generate_road_type('main', 3, 2)
        
        # Generate Secondary Roads
        for _ in range(num_secondary_roads):
            self.generate_road_type('secondary', 2, 1)
        
        # Generate Residential Streets
        for _ in range(num_residential_streets):
            self.generate_road_type('residential', 1, 1)

    def is_road_crossing_park(self, x1, y1, x2, y2):
        for park in self.park_drawings:
            if self.line_intersects_rect(x1, y1, x2, y2, park):
                return True
        return False

    def is_road_over_water(self, x1, y1, x2, y2):
        for water in self.water_drawings:
            if self.line_intersects_rect(x1, y1, x2, y2, water):
                return True
        return False

    def allow_road_bridge(self):
        return random.random() < 0.1

    def extend_road_length(self, x1, y1, x2, y2, factor=2):
        dx = x2 - x1
        dy = y2 - y1
        return x1 + factor * dx, y1 + factor * dy

    def draw_road(self, x1, y1, x2, y2, color, width):
        with self.canvas.before:
            Color(*color)  # Set color before drawing
            Line(points=[x1, y1, x2, y2], width=width)

    def on_touch_down(self, touch):
        if self.collide_point(*touch.pos):
            if self.current_tool == 'station':
                self.add_station(touch.x, touch.y)
                return True
            elif self.current_tool == 'named_station':
                self.add_station(touch.x, touch.y, self.station_name)
                return True
            elif self.current_tool == 'eraser':
                self.erase(touch.pos, self.get_pen_size())
                return True
            elif self.current_tool == 'demolish':
                self.select_station_for_demolition(touch.x, touch.y)
                return True
            elif self.current_tool == 'demolish_station':
                self.demolish_station(touch.x, touch.y)
                return True
            elif self.current_tool == 'demolish_label':
                self.demolish_label(touch.x, touch.y)
                return True
            elif self.current_tool in ['water', 'park']:
                color = Color(0.2, 0.4, 0.8, 1) if self.current_tool == 'water' else Color(0.3, 0.6, 0.3, 1)
                instruction = InstructionGroup()
                instruction.add(color)
                line = Line(points=(touch.x, touch.y), width=self.get_pen_size())
                instruction.add(line)
                container = self.water_drawings if self.current_tool == 'water' else self.park_drawings
                container.append(instruction)
                self.canvas.add(instruction)
                touch.ud['instruction'] = instruction
                return True
            elif self.current_tool == 'line':
                self.select_station(touch.x, touch.y)
                return True
        return super(CanvasWidget, self).on_touch_down(touch)

    def is_point_in_drawable_area(self, x, y):
        margin_bottom = 1
        margin_top = max(8, self.get_pen_size() / 1.75)
        return self.y + margin_bottom <= y <= self.top - margin_top

    def draw_user_line(self, start_pos, end_pos, color):
        key = tuple(sorted([start_pos, end_pos]))
        if key not in self.user_lines_dict:
            self.user_lines_dict[key] = []

        existing_lines = self.user_lines_dict[key]
        num_parallel_lines = len(existing_lines) + 1

        base_line_width = 10
        scale_factor = 2 / 3
        line_width = base_line_width * (scale_factor ** (num_parallel_lines - 1))
        offsets = self.calculate_parallel_offsets(start_pos, end_pos, num_parallel_lines, base_line_width)

        with self.canvas:
            Color(*color)
            offset = offsets[len(existing_lines)]
            start_pos_adjusted = self.apply_offset(start_pos, offset)
            end_pos_adjusted = self.apply_offset(end_pos, offset)
            line = Line(points=[start_pos_adjusted[0], start_pos_adjusted[1], end_pos_adjusted[0], end_pos_adjusted[1]], width=line_width)
            self.user_lines_dict[key].append(line)

        self.add_line_expense(start_pos, end_pos)

    def add_line_expense(self, start_pos, end_pos):
        length = self.distance(start_pos[0], start_pos[1], end_pos[0], end_pos[1])
        parent_screen = self.get_parent_screen()
        parent_screen.line_expenses += length / 5000  # 1/5000 of creation price per day
        parent_screen.update_daily_expenses()

    def calculate_parallel_offsets(self, start_pos, end_pos, num_parallel_lines, line_width):
        if num_parallel_lines == 1:
            return [(0, 0)]

        direction = (end_pos[0] - start_pos[0], end_pos[1] - start_pos[1])
        perpendicular_direction = (-direction[1], direction[0])
        length = (perpendicular_direction[0] ** 2 + perpendicular_direction[1] ** 2) ** 0.5
        unit_perpendicular = (perpendicular_direction[0] / length, perpendicular_direction[1] / length)

        offsets = []
        total_offset = line_width * (num_parallel_lines - 1)
        for i in range(num_parallel_lines):
            offset_magnitude = -total_offset / 2 + i * line_width
            offset = (unit_perpendicular[0] * offset_magnitude, unit_perpendicular[1] * offset_magnitude)
            offsets.append(offset)

        return offsets

    def apply_offset(self, pos, offset):
        return pos[0] + offset[0], pos[1] + offset[1]

    def is_line_between_points(self, line_points, point1, point2, tolerance=1e-5):
        def points_are_close(p1, p2, tol):
            return abs(p1[0] - p2[0]) < tol and abs(p1[1] - p2[1]) < tol

        point1, point2 = tuple(point1), tuple(point2)
        return (points_are_close((line_points[0], line_points[1]), point1, tolerance) and 
                points_are_close((line_points[2], line_points[3]), point2, tolerance)) or \
                (points_are_close((line_points[0], line_points[1]), point2, tolerance) and 
                points_are_close((line_points[2], line_points[3]), point1, tolerance))
    
    def add_station(self, x, y, name=""):
        with self.canvas:
            Color(0, 0, 0)
            outer_ellipse = Ellipse(pos=(x - 8, y - 8), size=(16, 16))
            Color(1, 1, 1)
            inner_ellipse = Ellipse(pos=(x - 4, y - 4), size=(8, 8))

        station = (x, y, outer_ellipse, inner_ellipse)
        if name:
            self.stations.append((x, y, outer_ellipse, inner_ellipse, None, None))  # Add None placeholders for label elements
            self.add_station_label(x, y, name)
        else:
            self.stations.append((x, y, outer_ellipse, inner_ellipse))

    def add_station_expense(self):
        parent_screen = self.get_parent_screen()
        parent_screen.station_expenses += 10000  # $10,000 per station per day
        parent_screen.update_daily_expenses()
    
    def redraw_station_labels(self):
        for station in self.stations:
            if len(station) > 5 and station[4] is not None and station[5] is not None:
                background, text_instruction = station[4], station[5]
                with self.canvas:
                    Color(0.8, 0.8, 0.8, 0.8)  # semi-transparent light grey
                    self.canvas.add(background)
                    self.canvas.add(text_instruction)

    def add_station_label(self, x, y, name):
        with self.canvas:
            label = CoreLabel(text=name, font_size=12, color=(0, 0, 0, 1))
            label.refresh()
            label_width, label_height = label.texture.size
            background_pos = (x - label_width // 2 - 5, y + 20 - 5)
            background_size = (label_width + 10, label_height + 10)
            Color(0.8, 0.8, 0.8, 0.8)  # semi-transparent light grey
            background = Rectangle(pos=background_pos, size=background_size)
            text_instruction = Rectangle(texture=label.texture, pos=(x - label_width // 2, y + 20), size=label.texture.size)

        station = list(self.stations[-1])  # Convert to list to modify
        if len(station) == 4:  # Check if it's not already extended
            station.extend([background, text_instruction])
        else:
            station[4], station[5] = background, text_instruction  # Assign only if the list has enough elements
        self.stations[-1] = tuple(station)  # Convert back to tuple

    def select_station(self, x, y):
        selected_station = None
        for station in self.stations:
            station_x, station_y = station[0], station[1]
            if self.distance(x, y, station_x, station_y) < 10:
                selected_station = station
                break

        if selected_station:
            if len(self.selected_stations) == 1 and self.selected_stations[0] == selected_station:
                # If the same station is selected twice, do nothing
                return
        
            self.selected_stations.append(selected_station)
            if len(self.selected_stations) == 2:
                self.connect_stations(self.selected_stations[0], self.selected_stations[1])
                self.selected_stations.clear()

    def connect_stations(self, station1, station2):
        if station1 == station2:
            return

        x1, y1 = station1[0], station1[1]
        x2, y2 = station2[0], station2[1]
        distance = np.sqrt((x2 - x1) ** 2 + (y2 - y1) ** 2)

        parent_screen = self.get_parent_screen()
        if isinstance(parent_screen, MainGameScreen):
            if not parent_screen.update_money_for_new_line(distance):
                return

        self.draw_user_line((x1, y1), (x2, y2), self.get_line_color())

        for station in [station1, station2]:
            if isinstance(station[2], Ellipse):
                self.canvas.remove(station[2])
                with self.canvas:
                    Color(0, 0, 0)
                    self.canvas.add(station[2])

            if isinstance(station[3], Ellipse):
                self.canvas.remove(station[3])
                with self.canvas:
                    Color(1, 1, 1)
                    self.canvas.add(station[3])
        self.redraw_station_labels()

    def on_touch_move(self, touch):
        if not self.is_point_in_drawable_area(touch.x, touch.y):
            return False

        if self.collide_point(*touch.pos):
            if self.current_tool == 'eraser':
                self.erase(touch.pos, self.get_pen_size())

            elif 'instruction' in touch.ud:
                line = touch.ud['instruction'].children[-1]
                last_point = line.points[-2:]
                if self.distance(touch.x, touch.y, last_point[0], last_point[1]) > 5:
                    line.points += [touch.x, touch.y]

            elif 'line_instruction' in touch.ud:
                line = touch.ud['line_instruction'].children[-1]
                last_point = line.points[-2:]
                if self.distance(touch.x, touch.y, last_point[0], last_point[1]) > 5:
                    line.points += [touch.x, touch.y]

        return super(CanvasWidget, self).on_touch_move(touch)

    def get_line_color(self):
        return self.color_wheel.selected_color

    def generate_filtered_color(self, existing_colors):
        while True:
            r, g, b = [random.uniform(0.3, 1) for _ in range(3)]
            if r + g + b < 2.5 and (r, g, b) not in existing_colors:
                return r, g, b

    def generate_control_points(self, x1, y1, x2, y2):
        dist = ((x2 - x1) ** 2 + (y2 - y1) ** 2) ** 0.5
        mid_x = (x1 + x2) / 2
        mid_y = (y1 + y2) / 2
        angle = random.uniform(0, np.pi)
        d = random.uniform(0.2, 0.5) * dist
        ctrl_x1 = mid_x + d * np.cos(angle)
        ctrl_y1 = mid_y + d * np.sin(angle)
        ctrl_x2 = mid_x - d * np.cos(angle)
        ctrl_y2 = mid_y - d * np.sin(angle)
        return ctrl_x1, ctrl_y1, ctrl_x2, ctrl_y2

    def calculate_bezier_points(self, points, num_points=100):
        curve = []
        for t in np.linspace(0, 1, num_points):
            curve.append(self.calculate_bezier_point(points, t))
        return curve

    def calculate_bezier_point(self, points, t):
        n = len(points) - 1
        x = sum(comb(n, i) * (t ** i) * ((1 - t) ** (n - i)) * points[i][0] for i in range(n + 1))
        y = sum(comb(n, i) * (t ** i) * ((1 - t) ** (n - i)) * points[i][1] for i in range(n + 1))
        return x, y

    def generate_and_plot_lines(self, num_lines):
        # Clear existing system-generated lines, labels, and stations
        self.clear_system_generated_content()

        lines = {}
        min_station_distance = 10.0
        min_edge_distance = 50

        filtered_colors = [self.generate_filtered_color([]) for _ in range(num_lines)]

        for i in range(num_lines):
            valid = False
            while not valid:
                x1, y1 = random.uniform(min_edge_distance, self.width - min_edge_distance), random.uniform(
                    min_edge_distance, self.height - min_edge_distance)
                x2, y2 = random.uniform(min_edge_distance, self.width - min_edge_distance), random.uniform(
                    min_edge_distance, self.height - min_edge_distance)
                if not self.is_point_in_any_drawing((x1, y1), 50) and not self.is_point_in_any_drawing((x2, y2), 50):
                    valid = True
            ctrl_x1, ctrl_y1, ctrl_x2, ctrl_y2 = self.generate_control_points(x1, y1, x2, y2)
            lines[i] = [(x1, y1), (ctrl_x1, ctrl_y1), (ctrl_x2, ctrl_y2), (x2, y2)]

        stations = {}
        for line_index, line_coords in lines.items():
            start_point = line_coords[0]
            end_point = line_coords[-1]
            stations[line_index] = [start_point, end_point]

            segment_lengths = [self.distance(line_coords[j][0], line_coords[j][1], line_coords[j + 1][0],
                                            line_coords[j + 1][1]) for j in range(len(line_coords) - 1)]
            total_length = sum(segment_lengths)
            min_station_distance_line = min_station_distance * (total_length / 100)
            num_stations_on_line = int(total_length / min_station_distance_line)
            t_values = sorted([random.uniform(0, 1) for _ in range(num_stations_on_line)])

            for t in t_values:
                points = np.array(line_coords)
                x_coord, y_coord = self.calculate_bezier_point(points, t)
                if not self.is_point_in_any_drawing((x_coord, y_coord), 50) and (x_coord, y_coord) not in stations[
                    line_index]:
                    stations[line_index].append((x_coord, y_coord))

            bezier_points = self.calculate_bezier_points(line_coords, 20)  # Reduced number of points
            color = Color(*filtered_colors[line_index])
            line_instruction = InstructionGroup()
            line_instruction.add(color)
            for j in range(len(bezier_points) - 1):
                line = Line(points=[bezier_points[j][0], bezier_points[j][1], bezier_points[j + 1][0],
                                    bezier_points[j + 1][1]], width=10)
                line_instruction.add(line)
            self.lines_instruction.add(line_instruction)
            self.system_lines.append(line_instruction)

        for line_stations in stations.values():
            for x, y in line_stations:
                # Check if the station already exists (to preserve manually added named stations)
                existing_station = next((station for station in self.stations if station[0] == x and station[1] == y),
                                        None)
                if existing_station:
                    outer_ellipse, inner_ellipse, *label_instructions = existing_station[2:]
                    self.lines_instruction.add(outer_ellipse)
                    self.lines_instruction.add(inner_ellipse)
                    for instruction in label_instructions:
                        self.lines_instruction.add(instruction)
                else:
                    station_name = generate_station_name()
                    station_instruction = InstructionGroup()
                    station_instruction.add(Color(0, 0, 0))
                    outer_ellipse = Ellipse(pos=(x - 8, y - 8), size=(16, 16))
                    station_instruction.add(outer_ellipse)
                    station_instruction.add(Color(1, 1, 1))
                    inner_ellipse = Ellipse(pos=(x - 4, y - 4), size=(8, 8))
                    station_instruction.add(inner_ellipse)
                    self.lines_instruction.add(station_instruction)
                    self.stations.append((x, y, outer_ellipse, inner_ellipse, station_instruction))
                    self.system_stations.append((x, y, outer_ellipse, inner_ellipse, station_instruction))

        self.canvas.add(self.lines_instruction)

        # Add station labels after all other elements
        for x, y, *station_info in self.stations:
            if len(station_info) < 5:  # Add label only if it doesn't exist
                station_name = generate_station_name()
                self.add_station_label(x, y, station_name)


    def on_touch_up(self, touch):
        if self.collide_point(*touch.pos) and self.current_tool == 'line':
            if len(self.selected_stations) == 2:
                parent_screen = self.get_parent_screen()
                station1, station2 = self.selected_stations
                x1, y1 = station1[0], station1[1]
                x2, y2 = station2[0], station2[1]
                distance = np.sqrt((x2 - x1) ** 2 + (y2 - y1) ** 2)
                if parent_screen.update_money_for_new_line(distance):
                    self.connect_stations(station1, station2)
                self.selected_stations.clear()
        return super(CanvasWidget, self).on_touch_up(touch)

    def remove_user_line(self, start, end):
        key = tuple(sorted([start, end]))
        if key not in self.user_lines_dict:
            return

        lines_to_remove = []
        for line in self.user_lines_dict[key]:
            if self.is_line_between_points(line.points, start, end):
                lines_to_remove.append(line)

        for line in lines_to_remove:
            self.canvas.remove(line)
            self.user_lines_dict[key].remove(line)

        if not self.user_lines_dict[key]:
            del self.user_lines_dict[key]

        self.remove_line_expense(start, end)

    def demolish_line(self, station1, station2):
        x1, y1 = station1[0], station1[1]
        x2, y2 = station2[0], station2[1]
        self.remove_user_line((x1, y1), (x2, y2))
        self.remove_line_expense((x1, y1), (x2, y2))
    
    def demolish_label(self, x, y):
        for i, station in enumerate(self.stations):
            if len(station) > 5:
                label_background, label_text = station[4], station[5]
                if label_background and label_text and self.distance(x, y, label_background.pos[0] + label_background.size[0] / 2, label_background.pos[1] + label_background.size[1] / 2) < 20:
                    self.canvas.remove(label_background)
                    self.canvas.remove(label_text)
                    station_list = list(station)
                    station_list[4], station_list[5] = None, None
                    self.stations[i] = tuple(station_list)
                    return

    def remove_line_expense(self, start_pos, end_pos):
        length = self.distance(start_pos[0], start_pos[1], end_pos[0], end_pos[1])
        parent_screen = self.get_parent_screen()
        parent_screen.line_expenses -= length / 5000  # Remove 1/5000 of creation price per day
        parent_screen.update_daily_expenses()

    def demolish_station(self, x, y):
        station_to_remove = None
        for station in self.stations:
            station_x, station_y = station[0], station[1]
            if self.distance(x, y, station_x, station_y) < 10:
                station_to_remove = station
                break

        if station_to_remove:
            # Find all lines connected to this station
            connected_lines = []
            for key in list(self.user_lines_dict.keys()):
                if station_to_remove[0:2] in key:
                    connected_lines.append(key)

            # Remove the connected lines
            for line in connected_lines:
                self.remove_user_line(line[0], line[1])

            # Remove the station from the canvas and list
            self.stations.remove(station_to_remove)
            self.canvas.remove(station_to_remove[2])  # Remove outer ellipse
            self.canvas.remove(station_to_remove[3])  # Remove inner ellipse

            if len(station_to_remove) > 4:
                label_background = station_to_remove[4]
                label_text = station_to_remove[5]
                if isinstance(label_background, Rectangle):
                    self.canvas.remove(label_background)  # Remove label background
                if isinstance(label_text, Rectangle):
                    self.canvas.remove(label_text)  # Remove label text

            self.remove_station_expense()

    def remove_station_expense(self):
        parent_screen = self.get_parent_screen()
        parent_screen.station_expenses -= 10000  # Remove $10,000 per station per day
        parent_screen.update_daily_expenses()

    def distance(self, x1, y1, x2, y2):
        return np.sqrt((x2 - x1) ** 2 + (y2 - y1) ** 2)

    def is_point_in_any_drawing(self, point, min_distance):
        for drawing in self.water_drawings:
            line_points = drawing.children[-1].points
            points = list(zip(line_points[::2], line_points[1::2]))
            for p1, p2 in zip(points[:-1], points[1:]):
                if self.point_line_distance(point, p1, p2) < min_distance:
                    return True
        return False
    
    def update_money_for_new_line(self, length):
        max_length = (self.width ** 2 + self.height ** 2) ** 0.5  # Diagonal length of the canvas
        max_cost = 5000000000  # $5 billion
        cost_per_unit_length = max_cost / max_length
        line_cost = length * cost_per_unit_length
        print(f"Line length: {length}, Line cost: {line_cost}")
    
        parent_screen = self.get_parent_screen()
        if isinstance(parent_screen, MainGameScreen):
            if parent_screen.money >= line_cost:
                parent_screen.money -= line_cost
                parent_screen.update_money_display()
                print(f"Money after deduction: {parent_screen.money}")
                return True
            else:
                parent_screen.show_insufficient_funds_popup(line_cost)
                return False
        return True  # Always return True for SandboxScreen

    def get_parent_screen(self):
        parent = self.parent
        while parent and not isinstance(parent, (MainGameScreen, SandboxScreen)):
            parent = parent.parent
        return parent
    
    def show_clear_confirmation(self):
        content = BoxLayout(orientation='vertical', padding=10, spacing=10)
        message = Label(text="Are you sure you want to clear the canvas?", font_size='20sp')
        buttons = BoxLayout(size_hint_y=0.3, spacing=10)

        yes_button = create_styled_button("Yes", size_hint_x=0.5)
        no_button = create_styled_button("No", size_hint_x=0.5)

        buttons.add_widget(yes_button)
        buttons.add_widget(no_button)

        content.add_widget(message)
        content.add_widget(buttons)

        self.popup = Popup(title="Confirm Clear", content=content, size_hint=(0.75, 0.5), auto_dismiss=False)
        
        # Customize the title color
        self.popup.title_color = [1, 1, 1, 1]  # Set the title color to white

        # Adding the orange line below the title
        with self.popup.canvas.before:
            Color(0.9, 0.521, 0.206, 1)  # Set the color to orange
            self.line = Rectangle(size=(self.popup.width, 2), pos=(self.popup.x, self.popup.y + self.popup.height))
        
        # Bind the position and size of the line to the popup
        self.popup.bind(on_open=self.update_line_position)
        self.popup.bind(on_dismiss=self.remove_line)
        
        self.popup.open()

        yes_button.bind(on_release=self.confirm_clear)
        no_button.bind(on_release=self.cancel_clear)

    def update_line_position(self, *args):
        self.line.size = (self.popup.width, 2)
        self.line.pos = (self.popup.x, self.popup.y + self.popup.height)

    def remove_line(self, *args):
        self.popup.canvas.before.remove(self.line)

    def confirm_clear(self, instance):
        self.clear_all()
        self.popup.dismiss()

    def cancel_clear(self, instance):
        self.popup.dismiss()
    
    def clear_all(self):
        self.canvas.clear()
        # Redrawing the white background
        with self.canvas:
            Color(1, 1, 1, 1)  # Set color to white
            self.bg_rect = Rectangle(pos=self.pos, size=self.size)
        # Resetting lists and dictionaries
        self.user_lines_dict.clear()
        self.water_drawings.clear()
        self.park_drawings.clear()
        self.stations.clear()
        self.selected_stations.clear()
        self.lines.clear()
        self.user_lines.clear()
        self.system_lines.clear()
        self.system_stations.clear()
        self.selected_demolish_stations.clear()
        self.heatmap_visible = False
        self.heatmap_instruction.clear()
        self.station_name = ""
        self.graph.clear()

        # Rebuild the graph if needed
        self.build_graph()
    
    def erase(self, pos, pen_size):
        with self.canvas:
            Color(1, 1, 1, 1)  # White color with no transparency
            touch_x, touch_y = pos
            Line(circle=(touch_x, touch_y, pen_size / 2), width=pen_size)
    
    def point_line_distance(self, point, start, end):
        if start == end:
            return self.distance(point[0], point[1], start[0], start[1])
        px, py = point[0] - start[0], point[1] - start[1]
        sx, sy = end[0] - start[0], end[1] - start[1]
        s_norm = sx ** 2 + sy ** 2
        proj = (px * sx + py * sy) / s_norm
        proj = max(0, min(1, proj))
        nearest_x = start[0] + proj * sx
        nearest_y = start[1] + proj * sy
        return self.distance(point[0], point[1], nearest_x, nearest_y)

    def select_station_for_demolition(self, x, y):
        selected_station = None
        for station in self.stations:
            station_x, station_y = station[0], station[1]
            if self.distance(x, y, station_x, station_y) < 10:
                selected_station = station
                break

        if selected_station and selected_station not in self.selected_demolish_stations:
            self.selected_demolish_stations.append(selected_station)
            if len(self.selected_demolish_stations) == 2:
                print(f"[select_station_for_demolition] Demolishing line between {self.selected_demolish_stations[0]} and {self.selected_demolish_stations[1]}")
                self.demolish_line(self.selected_demolish_stations[0], self.selected_demolish_stations[1])
                self.selected_demolish_stations.clear()

def comb(n, k):
    if k > n:
        return 0
    if k == 0 or k == n:
        return 1
    return comb(n - 1, k - 1) + comb(n - 1, k - 1)

class LineGeneratorApp(App):
    def build(self):
        sm = ScreenManager()
        sm.add_widget(MainMenu(name='main_menu'))
        sm.add_widget(SandboxScreen(name='sandbox'))
        sm.add_widget(MainGameScreen(name='main_game'))
        sm.add_widget(ControlsScreen(name='controls'))
        return sm

if __name__ == "__main__":
    LineGeneratorApp().run()
