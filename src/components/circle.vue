/* vim: set softtabstop=2 shiftwidth=2 expandtab : */

<script>

import _ from 'lodash';

import eventBinder from '../utils/eventsBinder.js'
import propsBinder from '../utils/propsBinder.js'

const props = {
    center: {
        type: Object,
        twoWay: true,
        required: true
    },
    radius: {
        type: Number,
        default: 1000,
        twoWay: true
    },
    bounds: {
        type: Object,
        default: {},
        twoWay: true
    },
    draggable: {
        type: Boolean,
        default: false,
    },
    editable: {
        type: Boolean,
        default: false,
    },
    options: {
        type: Object,
        default: {
            clickable: false,
            fillColor: "#000000",
            fillOpacity: 0.3,
            strokeColor: "#000000",
            strokeOpacity: 1.0,
            strokePosition: "CENTER",
            strokeWeight: 2,
            visible: true,
            zIndex: 1000
        }
    }
}

const events = [
    'center_changed',
    'click',
    'dblclick',
    'drag',
    'dragend',
    'dragstart',
    'mousedown',
    'mousemove',
    'mouseout',
    'mouseover',
    'mouseup',
    'radius_changed',
    'rightclick'
]

export default {
    props: props,

    ready () {
        this.$dispatch('register-circle', this);
    },

    methods: {
        createCircle (options, map) {
            this.circleObject = new google.maps.Circle(options);
            propsBinder(this, this.circleObject, props);
            eventsBinder(this, this.circleObject, events);
            this.mapAvailableDefered.resolve(map);

            const updateBounds = () => {
                this.bounds = this.circleObject.getBounds();
            }

            this.$watch('radius', updateBounds);
            // because center is an object and we need to be warned even if only the lat or lng change. not the whole reference
            this.$watch('center', updateBounds, {deep: true});
        },

        detached () {
            this.circleObject.setMap(null);
        }
    },

    events: {
        'map-ready' (map) {
            this.registrar = 'map';
            this.mapObject = map;
            const options = _.clone(this.$data);
            options.map = this.mapObject;
            delete options.bounds;
            this.createCircle(options, map);
        }
    }
}


</script>
