import com.riadalabs.jira.plugins.insight.services.model.ObjectAttributeBean
import com.riadalabs.jira.plugins.insight.services.model.ObjectAttributeValueBean
import com.riadalabs.jira.plugins.insight.services.model.MutableObjectAttributeBean
import com.riadalabs.jira.plugins.insight.services.model.MutableObjectBean
import com.atlassian.jira.component.ComponentAccessor


int organisation_modul = 1522
int organisation_bearbeiter = 1523
int modul_organisation = 1517
int modul_bearbeiter = 1518
int bearbeiter_modul = 1519
int bearbeiter_organisation = 1520

int bearbeiterTyp = 215
int organisationTyp = 216
int modulTyp = 214

def attributeBeans = object.getObjectAttributeBeans() 
log.warn("Key: " + object.getObjectTypeId().toString())

int typ = object.getObjectTypeId()
switch(typ){
	case bearbeiterTyp: isAktuell(getModule(getBean(attributeBeans, bearbeiter_modul)), modul_bearbeiter)
						isAktuell(getModule(getBean(attributeBeans, bearbeiter_organisation)), organisation_bearbeiter)
						break
    case organisationTyp: isAktuell(getModule(getBean(attributeBeans, organisation_modul)), modul_organisation)
						 isAktuell(getModule(getBean(attributeBeans, organisation_bearbeiter)), bearbeiter_organisation)
						 break
    case modulTyp: isAktuell(getModule(getBean(attributeBeans, modul_organisation)), organisation_modul)
						 isAktuell(getModule(getBean(attributeBeans, modul_bearbeiter)), bearbeiter_modul)
						 break
}



//Methods*************************************************************************

ObjectAttributeBean getBean(List<ObjectAttributeBean> attributeBeans, int id){
    ObjectAttributeBean result = null
    for (bean in attributeBeans){
        if(bean.getObjectTypeAttributeId() == id){
        	result = bean
			break
            }
        }
    return result
    }

List<ObjectAttributeValueBean> getModule(ObjectAttributeBean bean){
    def result = bean.getObjectAttributeValueBeans()
    return result
    }

void isAktuell(List<ObjectAttributeValueBean> module, int id){
    Class objectFacadeClass = ComponentAccessor.getPluginAccessor().getClassLoader().findClass("com.riadalabs.jira.plugins.insight.channel.external.api.facade.ObjectFacade")
	def objectFacade = ComponentAccessor.getOSGiComponentInstanceOfType(objectFacadeClass)
	    for(modul in module){
            int objectKey = modul.getValue()
            def modulObject = objectFacade.loadObjectBean(objectKey)
          List<ObjectAttributeValueBean> organizations = getModule(getBean(modulObject.getObjectAttributeBeans(),id))
            boolean vorhanden = false
          for(organization in organizations){
             if(organization.getValue() == object.getId()){
                 vorhanden = true
                 break
             }
              }
            if(!vorhanden){
                log.warn("nicht vorhanden")
                log.warn("objectBean: " + modulObject.getClass().toString())
                updateObject(modulObject, id)
                }

	}
}


def updateObject(MutableObjectBean objectBean, int id){
    
    def objectFacade = ComponentAccessor.getOSGiComponentInstanceOfType(ComponentAccessor.getPluginAccessor().getClassLoader().findClass("com.riadalabs.jira.plugins.insight.channel.external.api.facade.ObjectFacade"));
def objectTypeAttributeFacade = ComponentAccessor.getOSGiComponentInstanceOfType(ComponentAccessor.getPluginAccessor().getClassLoader().findClass("com.riadalabs.jira.plugins.insight.channel.external.api.facade.ObjectTypeAttributeFacade"));
def objectAttributeBeanFactory = ComponentAccessor.getOSGiComponentInstanceOfType(ComponentAccessor.getPluginAccessor().getClassLoader().findClass("com.riadalabs.jira.plugins.insight.services.model.factory.ObjectAttributeBeanFactory"));
  
def objectTypeAttributeBean = objectTypeAttributeFacade.loadObjectTypeAttributeBean(id).createMutable() //The id of the attribute
    ArrayList<String> oldValues = new ArrayList<>()
    oldValues.add(object.getId().toString())
    def oldBean = getBean(objectBean.getObjectAttributeBeans(), id)
    if(oldBean != null){
    for(value in oldBean.getObjectAttributeValueBeans()){
      oldValues.add(value.getValue().toString())
        }
    }
/* Create the new attribute bean based on the value */
def newObjectAttributeBean = objectAttributeBeanFactory.createObjectAttributeBeanForObject(objectBean, objectTypeAttributeBean, oldValues.toArray(new String[oldValues.size()]));
 log.warn("newOAB: " + newObjectAttributeBean)
/* Load the attribute bean */
def objectAttributeBean = objectFacade.loadObjectAttributeBean(objectBean.getId(), objectTypeAttributeBean.getId());
if (objectAttributeBean != null) {
    /* If attribute exist reuse the old id for the new attribute */
    newObjectAttributeBean.setId(objectAttributeBean.getId());
}
 /* Store the object attribute into Insight. */
try {
    objectTypeAttributeBean = objectFacade.storeObjectAttributeBean(newObjectAttributeBean);
} catch (Exception vie) {
    log.warn("Could not update object attribute due to validation exception:" + vie.getMessage());
}
    
    
    
    }


 
