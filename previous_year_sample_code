# LEGO type:standard slot:10 autostart

from spike import PrimeHub, ColorSensor, Motor, MotorPair
from spike.control import wait_for_seconds, Timer
from math import *

hub = PrimeHub()

motor_pair = MotorPair('A','C')
left_color_sensor = ColorSensor('B')
right_color_sensor = ColorSensor('D')
dozer = Motor('E')
forklift = Motor('F')

# def get_color_sensor(location='left'):
#    sensor = {'left': 'B', 'right': 'D'}
#    return ColorSensor(sensor[location])


# def get_motor(location='left'):
#    motor ={
#        'left': 'A',
#        'right': 'C',
#        'forklift': 'F',
#        'dozer':'E'
#    }
#    return Motor(motor[location])

# motor_pair = MotorPair('B','F')
# left_color_sensor = ColorSensor('E')
# right_color_sensor = ColorSensor('A')
# dozer = Motor('D')
# forklift = Motor('C')

DEBUG = True
HIGH_SPEED = 15
SPEED = 15
TURN_SPEED = 8
BLACK_SENSE_COUNT = 2
MAX_ANGLE_ERROR_CORRECTION = 20
MAX_STEERING_ERROR_CORRECTION = 20
MAX_STEERING = 15

DOZER_UP_POSITION = 20
DOZER_DOWN_POSITION = DOZER_UP_POSITION - 320

UP2_DOWN = -0.65
DOWN2_UP = - UP2_DOWN
UP2_CARRY = -0.25
DOWN2_CARRY = - UP2_CARRY
CARRY2_DROP = -0.2
CARRY2_DROPE = -0.3
CARRY2_UP= - UP2_CARRY
DROPE2_UP = - CARRY2_DROPE - CARRY2_UP
DROP2_UP = -( UP2_CARRY + CARRY2_DROP)
DROP2_CARRY = - CARRY2_DROP
DROPE2_CARRY = - CARRY2_DROPE

# DOZER_UP_POSITION = 175
# DOZER_DOWN_POSITION = 20

# FORKLIFT_UP_POSITION = 359
# FORKLIFT_DOWN_POSITION = 200
# FORKLIFT_CARRY_POSITION = 320
BLACK_COLOR_REFLECTION = 40

FORKLIFT_UP_POSITION = 115
FORKLIFT_DOWN_POSITION = FORKLIFT_UP_POSITION - 260
FORKLIFT_CARRY_POSITION = 200



def turn_left(angle, speed=TURN_SPEED):
    print('turn left by angle:', angle)
    wait_for_seconds(0.5)
    hub.motion_sensor.reset_yaw_angle()
    orientation = hub.motion_sensor.get_yaw_angle()
    correction = -4
    motor_pair.start_tank(-speed, speed)
    while abs(orientation) < abs(angle):
        orientation = hub.motion_sensor.get_yaw_angle() - correction
    motor_pair.stop()
    orientation = hub.motion_sensor.get_yaw_angle() - correction
    print("new orientation:", orientation)


def turn_right(angle, speed=TURN_SPEED):
    print('turn right by angle', angle)
    wait_for_seconds(0.5)
    hub.motion_sensor.reset_yaw_angle()
    orientation = hub.motion_sensor.get_yaw_angle()
    correction = -4
    motor_pair.start_tank(speed, -speed)
    while abs(orientation) < abs(angle):
        orientation = hub.motion_sensor.get_yaw_angle() - correction
    motor_pair.stop()
    orientation = hub.motion_sensor.get_yaw_angle() - correction
    print("new orientation:", orientation)

def move_straight_duration_with_yaw_correction(duration=1, speed=SPEED):
    hub.motion_sensor.reset_yaw_angle()
    print("move straight duration:", duration)
    timer = Timer()
    while timer.now() < duration:
        error = hub.motion_sensor.get_yaw_angle()
        correction = error * -1
        motor_pair.start_tank(int(speed+correction), int(speed-correction))
    motor_pair.stop()
    print("current timer:", timer.now())

def move_straight_duration_with_yaw_correction_and_acceleration(duration=1, speed=SPEED, acceleration=0.1):
    hub.motion_sensor.reset_yaw_angle()
    timer = Timer()
    now = timer.now()
    while now < duration:
        now = timer.now()
        error = hub.motion_sensor.get_yaw_angle()
        correction = error * -1
        motor_pair.start_tank(int(speed+correction), int(speed-correction))
        if now < duration/2:    # acceleration
            speed = speed*(1+acceleration)
            speed = min(speed, 100)
        elif speed > SPEED:
            speed = speed/(1+acceleration)
        print('time, speed:', now, speed)
    motor_pair.stop()

def mission_5_move_straight_duration_with_yaw_correction_and_acceleration(duration=1, speed=SPEED, acceleration=0.1):
    hub.motion_sensor.reset_yaw_angle()
    timer = Timer()
    now = timer.now()
    # while now < duration-2:
    #     steering = int((left_color_sensor.get_reflected_light()-50)*1.75)
    #     now = timer.now()
    #     if steering < 0:
    #         steering = -(min(abs(steering), MAX_STEERING))
    #     else:
    #         steering = (min(abs(steering), MAX_STEERING))
    #     motor_pair.start(steering=steering)

    while now < duration-2:
        now = timer.now()
        error = hub.motion_sensor.get_yaw_angle()
        correction = error * -1
        motor_pair.start_tank(int(speed+correction), int(speed-correction))
        if now < duration/2:    # acceleration
            speed = speed*(1+acceleration)
            speed = min(speed, 100)
        elif speed > SPEED:
            speed = speed/(1+acceleration)
        print('time, speed:', now, speed)
    speed = 15    
    while now < duration:
        now = timer.now()
        error = hub.motion_sensor.get_yaw_angle()
        correction = error * -1
        motor_pair.start_tank(int(speed+correction), int(speed-correction))
        if now < duration/2:    # acceleration
            speed = speed*(1+acceleration)
            speed = min(speed, 100)
        elif speed > SPEED:
            speed = speed/(1+acceleration)

        right_color = right_color_sensor.get_reflected_light()
        if  right_color < BLACK_COLOR_REFLECTION:
            print("right detected black")
            break
        print('time, speed:', now, speed)
    motor_pair.stop()
    move_straight_distance(7)
    


def move_straight_distance(distance=1, speed=SPEED, correction=0):
    motor_pair.move_tank(distance, 'cm', int(speed-correction), int(speed+correction))


def move_dozer(position, speed=20):
    if position == 'up':
        dozer.run_for_rotations(1, speed)
        #dozer.run_for_degrees(359, speed)
        #dozer.run_to_position(DOZER_UP_POSITION, direction="clockwise", speed=speed)
    elif position == 'down':
        dozer.run_for_rotations(-1, speed)
        #dozer.run_to_position(DOZER_DOWN_POSITION, direction="counterclockwise", speed=speed)

def dozer_moveup(angle=190, speed=SPEED):
    dozerInitialRaw = dozer.get_position()
    print('dozer moveup initial position', dozerInitialRaw)
    if abs(dozerInitialRaw - angle) < 10:
        return
    dozer.run_for_degrees(angle, speed)
    dozerInitialRaw = dozer.get_position()
    print("dozer up final position:", dozerInitialRaw)


def dozer_movedown(angle=190, speed=SPEED):
    dozerInitialRaw = dozer.get_position()
    print('dozer movedown initial position:', dozerInitialRaw)
    if abs(dozerInitialRaw - 10) < 10:
        return
    dozer.run_for_degrees(-angle, speed)
    dozerInitialRaw = dozer.get_position()
    print("dozer down final position:", dozerInitialRaw)



# def detect_black(sensor):
#    counter = 0
#    if (left_color_sensor.get_reflected_light() < BLACK_COLOR_REFLECTION):
#        counter += 1
#    if (left_color_sensor.get_reflected_light() < BLACK_COLOR_REFLECTION):
#        counter += 1
#    if counter > 1:
#        return True
#    return False

# def get_current_yaw():
#    return hub.motion_sensor.get_yaw_angle()

def turn_left_until_left_color_sensor_detect_black(angle, speed=TURN_SPEED):
    print('turn left until left color sensor detects black:', angle)
    wait_for_seconds(0.5)
    hub.motion_sensor.reset_yaw_angle()
    orientation = hub.motion_sensor.get_yaw_angle()
    correction = -4
    motor_pair.start_tank(-speed, speed)
    while abs(orientation) < abs(angle):
        orientation = hub.motion_sensor.get_yaw_angle() - correction
        if (left_color_sensor.get_reflected_light() < BLACK_COLOR_REFLECTION): #and abs(abs(orientation) - abs(angle)) < MAX_ANGLE_ERROR_CORRECTION:
            print("left sensor detected black color")
            break
    motor_pair.stop()


def turn_left_until_right_color_sensor_detect_black(angle, speed=TURN_SPEED):
    print('turn left until right color sensor detects black:', angle)
    # wait_for_seconds(0.5)
    hub.motion_sensor.reset_yaw_angle()
    orientation = hub.motion_sensor.get_yaw_angle()
    correction = -4
    motor_pair.start_tank(-speed, speed)
    while abs(orientation) < abs(angle):
        orientation = hub.motion_sensor.get_yaw_angle() - correction
        # if (left_color_sensor.get_reflected_light() < BLACK_COLOR_REFLECTION) and abs(abs(orientation) - abs(angle)) < MAX_ANGLE_ERROR_CORRECTION:
        if (right_color_sensor.get_reflected_light() < BLACK_COLOR_REFLECTION):
            print("right sensor detected black color")
            break
    motor_pair.stop()


def turn_right_until_left_color_sensor_detect_black(angle, speed=TURN_SPEED):
    print('turn right until left color sensor detects black:', angle)
    # wait_for_seconds(0.5)
    hub.motion_sensor.reset_yaw_angle()
    orientation = hub.motion_sensor.get_yaw_angle()
    correction = -4
    motor_pair.start_tank(speed, -speed)
    while abs(orientation) < abs(angle):
        orientation = hub.motion_sensor.get_yaw_angle() - correction
        # if (left_color_sensor.get_reflected_light() < BLACK_COLOR_REFLECTION) and abs(abs(orientation) - abs(angle)) < MAX_ANGLE_ERROR_CORRECTION:
        if (left_color_sensor.get_reflected_light() < BLACK_COLOR_REFLECTION):
            print("left sensor detected black color")
            break
    motor_pair.stop()


def turn_right_until_right_color_sensor_detect_black(angle, speed=TURN_SPEED, min_angle=0):
    print('turn right until right color sensor detects black:', angle)
    # wait_for_seconds(0.5)
    hub.motion_sensor.reset_yaw_angle()
    orientation = hub.motion_sensor.get_yaw_angle()
    correction = -4
    motor_pair.start_tank(speed, -speed)
    while abs(orientation) < abs(angle):
        orientation = hub.motion_sensor.get_yaw_angle() - correction
        # if (right_color_sensor.get_reflected_light() < BLACK_COLOR_REFLECTION) and abs(abs(orientation) - abs(angle)) < MAX_ANGLE_ERROR_CORRECTION:
        if (right_color_sensor.get_reflected_light() < BLACK_COLOR_REFLECTION):
            print("right sensor detected black color")
            break
    if orientation < min_angle:
        turn_right(min_angle-orientation)
        turn_right_until_right_color_sensor_detect_black(angle - min_angle)
    motor_pair.stop()

def move_straight_yaw_until_right_sensor_detects_black(duration=1, speed=SPEED):
    print('move straight yaw until right color sensor detects black:', duration)
    hub.motion_sensor.reset_yaw_angle()
    # right_color = right_color_sensor.get_reflected_light()
    if duration > 2:
        move_straight_duration_with_yaw_correction(duration-2)

    timer = Timer()
    while True:
        right_color = right_color_sensor.get_reflected_light()
        current_timer = timer.now()
        print('current timer', current_timer, right_color)
        if current_timer > 2:
            print("time expired")

            break
        if abs(current_timer) <=2 and right_color < BLACK_COLOR_REFLECTION:
            print("right detected black")
            break
        error = hub.motion_sensor.get_yaw_angle()
        correction = error * -2
        motor_pair.start_tank(int(speed+correction), int(speed-correction))
    motor_pair.stop()

def move_straight_yaw_until_left_sensor_detects_black(duration=1, speed=SPEED):
    print('move straight yaw until left color sensor detects black:', duration)
    hub.motion_sensor.reset_yaw_angle()
    if duration > 2:
        move_straight_duration_with_yaw_correction(duration-2)

    timer = Timer()
    while True:
        left_color = left_color_sensor.get_reflected_light()

        current_timer = timer.now()
        print('current timer', current_timer, left_color)
        if current_timer > 2:
            print("left timer expired")
            break
        if current_timer <= 2 and left_color < BLACK_COLOR_REFLECTION:
            print("left color detected black")
            break
        error = hub.motion_sensor.get_yaw_angle()
        correction = error * -2
        motor_pair.start_tank(int(speed+correction), int(speed-correction))
    motor_pair.stop()



def follow_black_line_left_sensor_for_duration(duration):
    print('follow black line on left sensor for duration:', duration)
    motor_pair.set_default_speed(SPEED)
    timer = Timer()

    while timer.now() < duration:
        steering = int((left_color_sensor.get_reflected_light()-50)*1.75)

        if steering < 0:
            steering = -(min(abs(steering), MAX_STEERING))
        else:
            steering = (min(abs(steering), MAX_STEERING))
        motor_pair.start(steering=steering)

    motor_pair.stop()

def follow_black_line_right_sensor_for_duration(duration):
    print('follow black line on right sensor for duration:', duration)
    motor_pair.set_default_speed(SPEED)
    timer = Timer()

    while timer.now() < duration:
        steering = int((right_color_sensor.get_reflected_light()-50)*1.75)

        if steering < 0:
            steering = -(min(abs(steering), MAX_STEERING))
        else:
            steering = (min(abs(steering), MAX_STEERING))
        motor_pair.start(steering=steering)

    motor_pair.stop()




def follow_black_line_until_right_detects_black(duration=3):
    print('follow black line on right sensor detects black:', duration)

    motor_pair.set_default_speed(SPEED)
    timer = Timer()

    while right_color_sensor.get_reflected_light() > BLACK_COLOR_REFLECTION or timer.now() < duration:
        steering = int((left_color_sensor.get_reflected_light()-50)*1.75)
        if steering < 0:
            steering = -(min(abs(steering), MAX_STEERING))
        else:
            steering = (min(abs(steering), MAX_STEERING))
        motor_pair.start(steering)
    motor_pair.stop()


def move_forklift_bak(position, speed=SPEED):
    if position == 'up':
        forklift.run_to_position(FORKLIFT_UP_POSITION, direction="clockwise", speed=speed)
    elif position == 'carry':
        forklift.run_to_position(FORKLIFT_CARRY_POSITION, speed=speed)
    elif position == 'down':
        forklift.run_to_position(FORKLIFT_DOWN_POSITION, direction="counterclockwise", speed=speed)


# def move_forklift(position, speed=SPEED):
#     if position == 'up':
#         forklift.run_for_rotations(1, speed)
#     elif position == 'carry':
#         forklift.run_for_rotations(-0.25, speed)
#     elif position == 'down':
#         forklift.run_for_rotations(-1, speed)
#     elif position == 'drop':
#         forklift.run_for_rotations(-1, speed)


# def move_dozer(position, speed=None):
#    if position == 'up':
#        dozer.run_for_degrees(180, speed)
#        dozer.run_to_position(DOZER_UP_POSITION, direction="clockwise", speed=speed)mission_5_move_straight_duration_with_yaw_correction_and_acceleration
#    elif position == 'down':
#        dozer.run_for_degrees(-180, speed)
#        dozer.run_to_position(DOZER_DOWN_POSITION, direction="counterclockwise", speed=speed)


def forklift_moveup(angle=190, speed=None):
    # forklift.set_default_speed(SPEED)
    if speed is None:
        speed = SPEED
    forkliftInitialRaw = forklift.get_position()
    print(forkliftInitialRaw)
    if abs(forkliftInitialRaw - 355) < 10:
        return
    forklift.run_for_degrees(angle,speed)

def forklift_movedown(angle=190, speed=None):
    # forklift.set_default_speed(SPEED)
    if speed is not None:
        speed = SPEED
        forklift.set_default_speed(speed)
    forkliftInitialRaw = forklift.get_position()
    print(forkliftInitialRaw)
    if abs(forkliftInitialRaw - 160) < 20:
        return
    forklift.run_for_degrees(angle,speed)

def move_straight_duration_with_yaw_correction_and_acceleration_until_right_sensor_detects_black(duration=1, speed=SPEED, acceleration=0.1):
    hub.motion_sensor.reset_yaw_angle()
    timer = Timer()
    now = timer.now()
    while now < duration:
        now = timer.now()
        error = hub.motion_sensor.get_yaw_angle()
        correction = error * -1
        motor_pair.start_tank(int(speed+correction), int(speed-correction))
        if now < duration/2:    # acceleration
            speed = speed*(1+acceleration)
            speed = min(speed, 100)
        elif speed > SPEED:
            speed = speed/(1+acceleration)

        right_color = right_color_sensor.get_reflected_light()
        if abs(duration-now) <=1 and right_color < BLACK_COLOR_REFLECTION:
            print("right detected black")
            break
        print('time, speed:', now, speed)
    motor_pair.stop()


def move_straight_duration_with_yaw_correction_and_acceleration_until_left_sensor_detects_black(duration=1, speed=SPEED, acceleration=0.1):
    hub.motion_sensor.reset_yaw_angle()
    timer = Timer()
    now = timer.now()
    while now < duration:
        now = timer.now()
        error = hub.motion_sensor.get_yaw_angle()
        correction = error * -1
        motor_pair.start_tank(int(speed+correction), int(speed-correction))
        if now < duration/2:    # acceleration
            speed = speed*(1+acceleration)
            speed = min(speed, 100)
        elif speed > SPEED:
            speed = speed/(1+acceleration)

        left_color = left_color_sensor.get_reflected_light()
        if abs(duration-now) <=1 and left_color < BLACK_COLOR_REFLECTION:
            print("left detected black")
            break
        print('time, speed:', now, speed)
    motor_pair.stop()


def move_straight_duration_with_yaw_correction_and_steering_until_right_sensor_detects_black(duration=1, speed=SPEED, steering=5):
    hub.motion_sensor.reset_yaw_angle()
    timer = Timer()
    now = timer.now()
    while now < 1:
        now = timer.now()
        error = hub.motion_sensor.get_yaw_angle()
        correction = error * -1
        motor_pair.start_tank(int(speed+correction), int(speed-correction))
        print('time, speed:', now, speed)
    #while (right_color_sensor.get_reflected_light() > BLACK_COLOR_REFLECTION and now > (duration -2) ) or now < duration:
    while now < duration:
        now = timer.now()
        if(right_color_sensor.get_reflected_light() < BLACK_COLOR_REFLECTION and now > (duration -2) ):
            break
        # error = hub.motion_sensor.get_yaw_angle()
        # correction = error * -1
        motor_pair.start(steering,speed)
        #motor_pair.start_tank(int(speed+correction-steering), int(speed-correction+steering))
        print('time, speed:', now, speed)
    motor_pair.stop()

def mission03_energy_storage_back():

    move_straight_duration_with_yaw_correction_and_acceleration_until_right_sensor_detects_black(3,15,0.04)
    turn_right(25,3)
    move_straight_duration_with_yaw_correction_and_acceleration_until_right_sensor_detects_black(3,12,0.04)
    move_straight_distance(0.7)
    turn_left(10)
    #turn_left_until_left_color_sensor_detect_black(20,3)
    #wait_for_seconds(0.5)

    # turn_right(20forklift_movedown

    # #forklift.run_for_degrees(-90,10)
    forklift_movedown(-150, 2)
    forklift_moveup(150, 20)

def mission03_energy_storage():

    move_straight_duration_with_yaw_correction_and_acceleration_until_right_sensor_detects_black(4.5,15,0)
    turn_right(25,3)
    move_straight_duration_with_yaw_correction_and_acceleration_until_right_sensor_detects_black(4,15,0)
    move_straight_distance(1)
    #turn_left(20)
    turn_left_until_left_color_sensor_detect_black(20,3)
    turn_left(6,5)
    #wait_for_seconds(0.5)
    #move_straight_distance(-2)

    #wait_for_seconds(0.5)

    # turn_right(20forklift_movedown

    # #forklift.run_for_degrees(-90,10)
    #forklift_movedown(-100, 80)
    forklift.run_for_rotations(CARRY2_DROP, 20)
    move_straight_distance(-3)
    forklift.run_for_rotations(DROP22_UP, 20)
    #forklift_moveup(100, 70)


def mission04_solar_farm():
    #forklift_moveup(60, 100)
    #forklift_movedown(-60, 100)
    # move_forklift('up')
    # move_straight_yaw_until_right_sensor_detects_black(3)
    # turn_right(25)
    # move_straight_yaw_until_right_sensor_detects_black(4.5)
    #turn_right_until_right_color_sensor_detect_black(80)
    turn_right(60)
    move_straight_distance(27,45)
    # motor_pair.move(25, steering=14, speed=SPEED)
    # turn_right(30)
    turn_right(40)
    move_straight_distance(2)
    wait_for_seconds(0.5)
    move_straight_distance(-17,40)
    move_straight_distance(2)
    turn_right(110)
    move_straight_duration_with_yaw_correction_and_acceleration(3,50,0)
    #forklift_movedown(-160)
    #forklift_movedown(180)

    # turn_left(20)

    # move_straight_distance(15)
    # turn_right(30)
    # forklift_moveup(160)
    # motor_pair.move_tank(1, 'cm', SPEED, SPEED)
    # turn_right_until_left_color_sensor_detect_black(60)
    # move_straight_distance(5)
    # turn_right(90)
    # move_straight_distance(-15)
    # dozer_movedown(380)
    # move_straight_distance(-10)
    # dozer_moveup(380)

def mission05_smartgrid_fast():
    move_straight_duration_with_yaw_correction_and_acceleration(3, 47,0)
    #mission_5_move_straight_duration_with_yaw_correction_and_acceleration(5,40,0)
    # move_straight_duration_with_yaw_correction_and_acceleration(3,40,0.0)
    # move_straight_yaw_until_right_sensor_detects_black(3,20)
    #move_straight_distance(3)
    turn_left(85)
    #turn_right_until_left_color_sensor_detect_black(90,3)
    #move_straight_duration_with_yaw_correction_and_steering_until_right_sensor_detects_black(4,35,0)
    move_straight_duration_with_yaw_correction_and_acceleration(3,-32,0.0)
    move_straight_distance(1.5)
    #mission_5_move_straight_duration_with_yaw_correction_and_acceleration(4,35,0)
    # move_straight_duration_with_yaw_correction_and_acceleration(2,40,0)
    # move_straight_duration_with_yaw_correction_and_acceleration_until_right_sensor_detects_black(2,15,0)
    # move_straight_distance(3)
    # turn_right(90)
    # move_straight_distance(-4)
    move_dozer('down')
    move_straight_distance(5)
    move_dozer('up')
    # move_straight_distance(-3)
    # turn_left(82)
    move_straight_distance(25, 40)
    turn_left(70)
    move_straight_distance(55, 60)

def mission05_smartgrid():
    move_straight_distance(5)
    turn_right(77)
    #turn_right_until_left_color_sensor_detect_black(90,3)
    #move_straight_duration_with_yaw_correction_and_steering_until_right_sensor_detects_black(4,35,0)
    mission_5_move_straight_duration_with_yaw_correction_and_acceleration(4,35,0)
    # move_straight_duration_with_yaw_correction_and_acceleration(2,40,0)
    # move_straight_duration_with_yaw_correction_and_acceleration_until_right_sensor_detects_black(2,15,0)
    move_straight_distance(3)
    turn_right(90)
    move_straight_distance(-4)
    move_dozer('down')
    move_straight_distance(5)
    move_dozer('up')
    move_straight_distance(-3)
    turn_left(82)
    move_straight_distance(35, 40)
    turn_right(65)
    move_straight_distance(46, 60)

def mission05_smartgrid_bak():
    move_straight_distance(12)
    turn_right(30)
    move_straight_duration_with_yaw_correction_and_acceleration_until_left_sensor_detects_black(2)
    move_straight_duration_with_yaw_correction_and_acceleration_until_right_sensor_detects_black(2)
    move_straight_distance(7)
    turn_right(57)
    move_straight_distance(-5)
    move_dozer('down')
    move_straight_distance(5)
    move_dozer('up')
    move_straight_distance(-5)
    turn_left(85)
    move_straight_distance(30, 40)
    turn_right(65)
    move_straight_distance(47, 40)

def mission07_wind_turbine_bak():
    move_straight_yaw_until_right_sensor_detects_black(6.5, 15)
    move_straight_distance(-1)

    turn_right(40)
    move_straight_distance(10)
    move_straight_distance(-10)
    move_straight_distance(11)
    move_straight_distance(-10)
    move_straight_distance(-11)


def mission07_wind_turbine():
    #move_straight_duration_with_yaw_correction_and_acceleration_until_right_sensor_detects_black(3,10,0.1)
    move_straight_distance(40,50)
    turn_right(80)
    move_straight_duration_with_yaw_correction(2,20)
    wait_for_seconds(1)
    move_straight_duration_with_yaw_correction(1,-30)
    move_straight_duration_with_yaw_correction(2,35)
    wait_for_seconds(1)
    move_straight_duration_with_yaw_correction(1,-30)
    move_straight_duration_with_yaw_correction(2,40)
    move_straight_duration_with_yaw_correction(2,-12)
    
   
    # move_straight_distance(10)
    # move_straight_distance(-10,40)
    # move_straight_distance(11)
    # move_straight_distance(-10,40)
    # move_straight_distance(11)
    # move_straight_distance(-11,40)
    turn_right(90)
    move_straight_duration_with_yaw_correction(2,60)
    #move_straight_distance(30,100)
    #move_forklift('carry')

def mission07_wind_turbine_from_back():
    #move_straight_duration_with_yaw_correction_and_acceleration_until_right_sensor_detects_black(3,10,0.1)
    move_straight_distance(40,-50)
    turn_left(80)
    move_straight_duration_with_yaw_correction(2,-20)
    wait_for_seconds(1)
    move_straight_duration_with_yaw_correction(1,30)
    move_straight_duration_with_yaw_correction(2,-35)
    wait_for_seconds(1)
    move_straight_duration_with_yaw_correction(1,30)
    move_straight_duration_with_yaw_correction(2,-40)
    move_straight_duration_with_yaw_correction(2,12)


    # move_straight_distance(10)
    # move_straight_distance(-10,40)
    # move_straight_distance(11)
    # move_straight_distance(-10,40)
    # move_straight_distance(11)
    # move_straight_distance(-11,40)
    turn_left(90)
    move_straight_duration_with_yaw_correction(2,-60)
    #move_straight_distance(30,100)
    #move_forklift('carry')



def mission08_watch_television():
    #move_straight_distance(30, 40)
    move_straight_duration_with_yaw_correction(3,40)
    move_straight_duration_with_yaw_correction(3,-40)
    # move_straight_distance(-25, 100)
    forklift.run_for_rotations(UP2_CARRY, 20)
    #dozer_movedown(DOZER_DOWN_POSITION)


def mission14_toy_factory():
    move_straight_duration_with_yaw_correction_and_acceleration(3,28,0)
    #move_straight_distance(35,15)
    forklift.run_for_rotations(CARRY2_DROP, 50)
    wait_for_seconds(0.5)
    forklift.run_for_rotations(DROP2_CARRY, 20)
    forklift.run_for_rotations(CARRY2_DROP, 50)
    forklift.run_for_rotations(DROP2_CARRY, 20)
    # forklift.run_for_rotations(CARRY2_DROP, 50)
    # forklift.run_for_rotations(DROP2_CARRY, 20)
    forklift.run_for_rotations(CARRY2_UP, 50)
    # forklift_movedown(100,20)
    # #wait_for_seconds(1)
    # forklift_moveup(80,5)
    # forklift_movedown(80,100)
    # #wait_for_seconds(1)
    # forklift_moveup(80,5)
    # forklift_movedown(80,100)
    # #wait_for_seconds(1)
    # forklift_moveup(80,50)
    move_straight_duration_with_yaw_correction_and_acceleration(2,-50,0)
    #move_straight_distance(-25,100)
    # move_forklift('carry')
    

def mission14_toy_factory_back():
    # move_straight_duration_with_yaw_correction_and_acceleration(2,25,0)
    # turn_left(30)
    move_straight_duration_with_yaw_correction_and_acceleration(8,35,0)
    #dozer_moveup()
    #turn_right(80)
    #move_straight_duration_with_yaw_correction_and_acceleration(1,11,0)
    forklift_movedown(-100,0)
    wait_for_seconds(1)
    forklift_moveup(80,5)
    forklift_movedown(-60,100)
    wait_for_seconds(1)
    forklift_moveup(60,5)
    forklift_movedown(-60,100)
    wait_for_seconds(1)
    forklift_moveup(100,50)
    #dozer_moveup()
    turn_right(70)

def missionx_dinosaur():
    move_straight_duration_with_yaw_correction_and_acceleration(2,35,0.0)
    turn_right(40, 40)
    move_straight_duration_with_yaw_correction_and_acceleration(3,30,0.0)
    turn_left(55,40)
    move_straight_distance(10, 40)
    move_dozer('up')
    move_straight_duration_with_yaw_correction_and_acceleration(3,50,0.0)
    forklift.run_for_rotations(UP2_CARRY, 20)



def mission10_power_plant_back():
    # forklift_movedown(-40)
    # move_straight_distance(25)
    move_straight_yaw_until_right_sensor_detects_black(5,15)
    wait_for_seconds(0.5)
    move_straight_distance(-5)
    wait_for_seconds(0.5)
    turn_left(7)
    forklift_movedown(-200)
    wait_for_seconds(0.5)
    move_straight_distance(5)
    # forklift_movedown(-50,50)
    #forklift.run_for_degrees(-70, 70)
    #move_straight_distance(1)
    #forklift_moveup(20, 20)
    # turn_left(5)
    # move_straight_distance(1)
    forklift_moveup(200,100)
    move_straight_distance(-5)
    wait_for_seconds(0.5)
    turn_right(10)
    wait_for_seconds(0.5)
    move_straight_distance(5,10)
    forklift_movedown(-200)
    forklift_moveup(200,100)
    #forklift_moveup(230,50)

def mission10_power_plant():
    forklift_movedown(-300,0)
    wait_for_seconds(1)
    forklift_moveup(300,100)
    # forklift_movedown(-80,100)
    # wait_for_seconds(1)
    # forklift_moveup(80,5)
    # forklift_movedown(-80,100)
    # wait_for_seconds(1)
    # forklift_moveup(80,50)
    # move_straight_distance(-25,15)

# forklift_movedown(-60,100)

def electrical_car():
    forklift.run_for_rotations(UP2_DOWN, 100)
    move_straight_duration_with_yaw_correction_and_acceleration(3,45,0)
    forklift.run_for_rotations(DROP2_UP, 80)
    wait_for_seconds(2)
    move_straight_duration_with_yaw_correction_and_acceleration(3,-45,0)
    forklift.run_for_rotations(DOWN2_UP, 100)

def mission03_energy_fast():
    #forklift.run_for_rotations(0.1, 20)
    #move_straight_duration_with_yaw_correction_and_steering(3,37, 5)
    move_straight_duration_with_yaw_correction_and_steering_until_right_sensor_detects_black(5,30,5)
    move_straight_distance(-6)
    # return
    # move_straight_duration_with_yaw_correction_and_acceleration(2,45,0)
    # move_straight_duration_with_yaw_correction_and_acceleration_until_right_sensor_detects_black(2,20,0)
    #forklift.run_for_rotations(-0.55, 100)
    # turn_left(25)
    forklift.run_for_rotations(CARRY2_DROPE, 5)
    wait_for_seconds(1)
    forklift.run_for_rotations(DROPE2_CARRY, 100)
    # move_straight_distance(-3)
    # forklift.run_for_rotations(0.55, 20)

    # move_straight_duration_with_yaw_correction_and_acceleration(2,45,0)
    # move_straight_duration_with_yaw_correction_and_acceleration_until_right_sensor_detects_black(2,20,0)
    #forklift.run_for_rotations(-0.55, 100)
    # turn_left(25)
    # forklift.run_for_rotations(CARRY2_DROPE, 10)
    # wait_for_seconds(1)
    # forklift.run_for_rotations(DOWN2_UP, 100)
    # move_straight_distance(-3)
    # forklift.run_for_rotations(0.55, 20)
    # turn_right(25,3)
    # move_straight_duration_with_yaw_correction_and_acceleration_until_right_sensor_detects_black(4,15,0)
    # move_straight_distance(1)
    # #turn_left(20)
    # turn_left_until_left_color_sensor_detect_black(20,3)
    # turn_left(6,5)
    #wait_for_seconds(0.5)
    #move_straight_distance(-2)

    #wait_for_seconds(0.5)

    # turn_right(20forklift_movedown

    # #forklift.run_for_degrees(-90,10)
    #forklift_movedown(-100, 80)
    # forklift.run_for_rotations(CARRY2_DROP, 20)
    # move_straight_distance(-3)
    # forklift.run_for_rotations(DROP2_UP, 20)
    #forklift_moveup(100, 70)

def group1():
    #move_forklift('carry')
    #mission03_energy_storage()
    mission03_energy_fast()
    mission04_solar_farm()
    #mission05_smartgrid()

def group2():
    #move_forklift('carry')
    mission07_wind_turbine()
    wait_for_seconds(5)
    mission08_watch_television()
    wait_for_seconds(10)
    mission14_toy_factory()
    wait_for_seconds(10)
    mission05_smartgrid_fast()
    move_dozer('down')
    wait_for_seconds(14)
    missionx_dinosaur()
    wait_for_seconds(14)
    group1()
    forklift.run_for_rotations(-UP2_CARRY, 20)
    #electrical_car()
    


print("mission ################################################")
#move_forklift('carry')
#forklift.run_for_rotations(0.6, 20)
#mission03_energy_fast()
#forklift.run_for_rotations(UP2_DOWN, 100)
#forklift.run_for_rotations( DOWN2_CARRY, 50)
#forklift.run_for_rotations(-UP2_CARRY, 50)
#mission05_smartgrid()
#group1()
#mission03_energy_fast()
#forklift.run_for_rotations(-0.2, 50)
#forklift.run_for_rotations(0.1, 50)
#move_dozer('down')
#move_dozer('up')
#dozer_movedown()
#mission04_solar_farm()
#mission03_energy_storage()
#dozer_moveup(DOZER_UP_POSITION)
#dozer.run_for_rotations(0.75, 15)
#mission07_wind_turbine()
#move_dozer('up')
#mission08_watch_television()
#mission14_toy_factory()
#group1()
#mission05_smartgrid()
group2()
#mission07_wind_turbine_from_back()
#mission03_energy_fast()
#group1()
#move_dozer('up')
#group1()
#mission05_smartgrid_fast()
#forklift.run_for_rotations(-UP2_CARRY, 20)
#missionx_dinosaur()
#forklift.run_for_rotations(0.05, 20)
#electrical_car()
print("mission end ################################################")

