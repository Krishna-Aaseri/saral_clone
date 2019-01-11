# saral_clone
import requests

from pprint import pprint
    
BASENAME="http://saral.navgurukul.org/api"

def get(url,params={}):
    data=requests.get(url,params)
    resp= data.json()
    return resp

courses_url= BASENAME+"/courses"
courses_response = get(courses_url)

print ("\n@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@\n")
print ("\n-------------------------Welcome to SARAL------------------------\n")
print ("\n@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@\n")
while True:    

    courses_id_list=[]
    def all_courses():
        index=0

        while index<len(courses_response["availableCourses"]):
            courses = courses_response["availableCourses"][index]
            courses_name = courses["name"]
            courses_id= courses["id"]
            courses_id_list.append(courses_id)
            print(str(index)+".",courses_name)
    
            index+=1
    all_courses()
    def spaace():
        print ("---------------------------------------------------------")	
    spaace()
    def choice_course():

        reply = int(input(" yaha Choose karke koi bhi course  study kar sakte hai.course choose karne ke lie apko course Id dalni hogi.\n Your answer:- "))
        choice_courses = courses_id_list[reply]
        return choice_courses
    courses = choice_course()	
    spaace()


    exercises_url = BASENAME+"/courses/"+str(courses)+"/exercises"
    exercises_resp = get(exercises_url)
    
    slugList=[]

    def all_exercises():
        index=0



        while index<len(exercises_resp["data"]):
            exercises=exercises_resp["data"][index]
            parentExerciseId=exercises["parentExerciseId"]
            if parentExerciseId == None:
                exercises_name=exercises["name"]
                exercises_slug=exercises["slug"]
                slugList.append(exercises_slug)
                print(str(index)+"=",exercises_name)


            elif parentExerciseId != None:
                exercises_name=exercises["name"]
                exercises_slug=exercises["slug"]
                slugList.append(exercises_slug)
                print(str(index)+".",exercises_name)

                index1=0
                while index1<len(exercises["childExercises"]):
                    childExercise_name=exercises["childExercises"][index1]["name"]
                    childExercise_slug=exercises["childExercises"][index1]["slug"]
                    slugList.append(childExercise_slug)

                    print("\t"+str(index1)+"=",childExercise_name)
                    index1+=1

            index+=1
    all_exercises()		
    spaace()

    slug_url= BASENAME+"/courses/"+str(courses)+"/exercise/getBySlug"
    slug_resp=get(slug_url,{'slug': slugList[0]})

    print (slug_resp['content'])

    def content_from_slug():
        index=0
        while True:
            spaace()
            choice_exercise= str(input("Enter 'n' to go to next exercise or 'p' to go to previous exercise or \nTo exit from current cousre enter any key :- "))
            spaace()
            if choice_exercise == "n" and index < len(slugList)-1:
                slug_resp=get(slug_url,{'slug': slugList[index+1]})
                print (slug_resp['content'])
                index+=1


            elif choice_exercise == "p" and index >0:
            
                slug_resp= get(slug_url,{'slug': slugList[index-1]})
                print (slug_resp['content'])
                index-=1



            elif choice_exercise=="n" and index==len(slugList)-1:
                slug_resp= get(slug_url,{'slug': slugList[index]})
                print (slug_resp['content'])
                print ("\nSorry  next exerices nhi hai\n")


            elif choice_exercise=="p" and index== 0:
                slug_resp= get(slug_url,{'slug': slugList[index]})
                print (slug_resp['content'])
                print ("\n\nSorry previews exerices nhi hai\n")


            else:
                print("\n-------apne current course se exit choose kiya hai.--------\n")
        
                break
    content_from_slug()
    exit= input("Enter 'S' to go back to course or enter any key to exit:- ")
    spaace()
    if exit == "S":
        continue
    else:
        print("\n @@@@@@@@@@@@@@@@@@@@@@@@@--APNE EXIT CHOOSE KIYA HAI--@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@")
        break    


